---
title: Przejściowa obsługa błędów z ponownymi próbami gRPC
author: jamesnk
description: Dowiedz się, jak wykonywać odporne, odporne na uszkodzenia wywołania gRPC z ponownymi próbami w programie .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/25/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/retries
ms.openlocfilehash: 613386d1fedd8b1b04081e3240b8a3aaf7b37012
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589805"
---
# <a name="transient-fault-handling-with-grpc-retries"></a><span data-ttu-id="a0a72-103">Przejściowa obsługa błędów z ponownymi próbami gRPC</span><span class="sxs-lookup"><span data-stu-id="a0a72-103">Transient fault handling with gRPC retries</span></span>

<span data-ttu-id="a0a72-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a0a72-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="a0a72-105">gRPC ponownych prób to funkcja, która umożliwia klientom gRPC automatyczne ponawianie nieudanych wywołań.</span><span class="sxs-lookup"><span data-stu-id="a0a72-105">gRPC retries is a feature that allows gRPC clients to automatically retry failed calls.</span></span> <span data-ttu-id="a0a72-106">W tym artykule opisano, jak skonfigurować zasady ponawiania w celu odporności, odpornych na uszkodzenia aplikacji gRPC na platformie .NET.</span><span class="sxs-lookup"><span data-stu-id="a0a72-106">This article discusses how to configure a retry policy to make resilient, fault tolerant gRPC apps in .NET.</span></span>

<span data-ttu-id="a0a72-107">gRPC ponownych prób wymaga [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) w wersji 2.36.0-pre1 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="a0a72-107">gRPC retries requires [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0-pre1 or later.</span></span>

## <a name="transient-fault-handling"></a><span data-ttu-id="a0a72-108">Obsługa błędu przejściowego</span><span class="sxs-lookup"><span data-stu-id="a0a72-108">Transient fault handling</span></span>

<span data-ttu-id="a0a72-109">wywołania gRPC mogą być przerywane przez błędy przejściowe.</span><span class="sxs-lookup"><span data-stu-id="a0a72-109">gRPC calls can be interrupted by transient faults.</span></span> <span data-ttu-id="a0a72-110">Błędy przejściowe obejmują:</span><span class="sxs-lookup"><span data-stu-id="a0a72-110">Transient faults include:</span></span>

* <span data-ttu-id="a0a72-111">Chwilowa utrata łączności sieciowej.</span><span class="sxs-lookup"><span data-stu-id="a0a72-111">Momentary loss of network connectivity.</span></span>
* <span data-ttu-id="a0a72-112">Tymczasowa niedostępność usługi.</span><span class="sxs-lookup"><span data-stu-id="a0a72-112">Temporary unavailability of a service.</span></span>
* <span data-ttu-id="a0a72-113">Limity czasu z powodu obciążenia serwera.</span><span class="sxs-lookup"><span data-stu-id="a0a72-113">Timeouts due to server load.</span></span>

<span data-ttu-id="a0a72-114">Gdy wywołanie gRPC zostanie przerwane, klient zgłosi `RpcException` ze szczegółowymi informacjami o błędzie.</span><span class="sxs-lookup"><span data-stu-id="a0a72-114">When a gRPC call is interrupted, the client throws an `RpcException` with details about the error.</span></span> <span data-ttu-id="a0a72-115">Aplikacja kliencka musi przechwycić wyjątek i wybrać sposób obsługi błędu.</span><span class="sxs-lookup"><span data-stu-id="a0a72-115">The client app must catch the exception and choose how to handle the error.</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
try
{
    var response = await client.SayHelloAsync(
        new HelloRequest { Name = ".NET" });

    Console.WriteLine("From server: " + response.Message);
}
catch (RpcException ex)
{
    // Write logic to inspect the error and retry
    // if the error is from a transient fault.
}
```

<span data-ttu-id="a0a72-116">Duplikowanie logiki ponawiania prób w całej aplikacji jest pełne i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="a0a72-116">Duplicating retry logic throughout an app is verbose and error prone.</span></span> <span data-ttu-id="a0a72-117">Na szczęście klient .NET gRPC ma wbudowaną obsługę automatycznych ponownych prób.</span><span class="sxs-lookup"><span data-stu-id="a0a72-117">Fortunately the .NET gRPC client has a built-in support for automatic retries.</span></span>

## <a name="configure-a-grpc-retry-policy"></a><span data-ttu-id="a0a72-118">Skonfiguruj zasady ponawiania gRPC</span><span class="sxs-lookup"><span data-stu-id="a0a72-118">Configure a gRPC retry policy</span></span>

<span data-ttu-id="a0a72-119">Zasady ponawiania są konfigurowane po utworzeniu kanału gRPC:</span><span class="sxs-lookup"><span data-stu-id="a0a72-119">A retry policy is configured once when a gRPC channel is created:</span></span>

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    RetryPolicy = new RetryPolicy
    {
        MaxAttempts = 5,
        InitialBackoff = TimeSpan.FromSeconds(1),
        MaxBackoff = TimeSpan.FromSeconds(5),
        BackoffMultiplier = 1.5,
        RetryableStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

<span data-ttu-id="a0a72-120">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="a0a72-120">The preceding code:</span></span>

* <span data-ttu-id="a0a72-121">Tworzy `MethodConfig` .</span><span class="sxs-lookup"><span data-stu-id="a0a72-121">Creates a `MethodConfig`.</span></span> <span data-ttu-id="a0a72-122">Zasady ponawiania można skonfigurować dla metody i metody są dopasowywane przy użyciu `Names` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a0a72-122">Retry policies can be configured per-method and methods are matched using the `Names` property.</span></span> <span data-ttu-id="a0a72-123">Ta metoda jest konfigurowana z `MethodName.Default` , dlatego jest stosowana do wszystkich metod gRPC wywoływanych przez ten kanał.</span><span class="sxs-lookup"><span data-stu-id="a0a72-123">This method is configured with `MethodName.Default`, so it's applied to all gRPC methods called by this channel.</span></span>
* <span data-ttu-id="a0a72-124">Konfiguruje zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="a0a72-124">Configures a retry policy.</span></span> <span data-ttu-id="a0a72-125">Te zasady instruują klientów do automatycznego ponawiania prób gRPC wywołań zakończonych niepowodzeniem z kodem stanu `Unavailable` .</span><span class="sxs-lookup"><span data-stu-id="a0a72-125">This policy instructs clients to automatically retry gRPC calls that fail with the status code `Unavailable`.</span></span>
* <span data-ttu-id="a0a72-126">Konfiguruje utworzony kanał, aby używać zasad ponawiania próby przez ustawienie `GrpcChannelOptions.ServiceConfig` .</span><span class="sxs-lookup"><span data-stu-id="a0a72-126">Configures the created channel to use the retry policy by setting `GrpcChannelOptions.ServiceConfig`.</span></span>

<span data-ttu-id="a0a72-127">Klienci gRPC utworzeni przy użyciu kanału zostaną automatycznie ponowiać nieudane wywołania:</span><span class="sxs-lookup"><span data-stu-id="a0a72-127">gRPC clients created with the channel will automatically retry failed calls:</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a><span data-ttu-id="a0a72-128">Opcje ponowień gRPC</span><span class="sxs-lookup"><span data-stu-id="a0a72-128">gRPC retry options</span></span>

<span data-ttu-id="a0a72-129">W poniższej tabeli opisano opcje konfigurowania zasad ponawiania gRPC:</span><span class="sxs-lookup"><span data-stu-id="a0a72-129">The following table describes options for configuring gRPC retry policies:</span></span>

| <span data-ttu-id="a0a72-130">Opcja</span><span class="sxs-lookup"><span data-stu-id="a0a72-130">Option</span></span> | <span data-ttu-id="a0a72-131">Opis</span><span class="sxs-lookup"><span data-stu-id="a0a72-131">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="a0a72-132">Maksymalna liczba prób wywołania, łącznie z pierwotną próbą.</span><span class="sxs-lookup"><span data-stu-id="a0a72-132">The maximum number of call attempts, including the original attempt.</span></span> <span data-ttu-id="a0a72-133">Ta wartość jest ograniczona, ponieważ `GrpcChannelOptions.MaxRetryAttempts` wartości domyślne to 5.</span><span class="sxs-lookup"><span data-stu-id="a0a72-133">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="a0a72-134">Wartość jest wymagana i musi być większa niż 1.</span><span class="sxs-lookup"><span data-stu-id="a0a72-134">A value is required and must be greater than 1.</span></span> |
| `InitialBackoff` | <span data-ttu-id="a0a72-135">Początkowe opóźnienie wycofywania między ponownymi próbami.</span><span class="sxs-lookup"><span data-stu-id="a0a72-135">The initial backoff delay between retry attempts.</span></span> <span data-ttu-id="a0a72-136">Losowe opóźnienie między 0 a bieżącym wycofywania określa, kiedy zostanie podjęta kolejna próba ponowienia.</span><span class="sxs-lookup"><span data-stu-id="a0a72-136">A randomized delay between 0 and the current backoff determines when the next retry attempt is made.</span></span> <span data-ttu-id="a0a72-137">Po każdej próbie bieżąca wycofywania zostanie pomnożona przez `BackoffMultiplier` .</span><span class="sxs-lookup"><span data-stu-id="a0a72-137">After each attempt, the current backoff is multiplied by `BackoffMultiplier`.</span></span> <span data-ttu-id="a0a72-138">Wartość jest wymagana i musi być większa od zera.</span><span class="sxs-lookup"><span data-stu-id="a0a72-138">A value is required and must be greater than zero.</span></span> |
| `MaxBackoff` | <span data-ttu-id="a0a72-139">Wartość maksymalna wycofywania umieszcza górny limit na wartości wykładniczej wycofywania wzrostu.</span><span class="sxs-lookup"><span data-stu-id="a0a72-139">The maximum backoff places an upper limit on exponential backoff growth.</span></span> <span data-ttu-id="a0a72-140">Wartość jest wymagana i musi być większa od zera.</span><span class="sxs-lookup"><span data-stu-id="a0a72-140">A value is required and must be greater than zero.</span></span> |
| `BackoffMultiplier` | <span data-ttu-id="a0a72-141">Wycofywania zostanie pomnożona przez tę wartość po każdej próbie ponowienia i zwiększy się wykładniczo, gdy mnożnik jest większy niż 1.</span><span class="sxs-lookup"><span data-stu-id="a0a72-141">The backoff will be multiplied by this value after each retry attempt and will increase exponentially when the multiplier is greater than 1.</span></span> <span data-ttu-id="a0a72-142">Wartość jest wymagana i musi być większa od zera.</span><span class="sxs-lookup"><span data-stu-id="a0a72-142">A value is required and must be greater than zero.</span></span> |
| `RetryableStatusCodes` | <span data-ttu-id="a0a72-143">Kolekcja kodów stanu.</span><span class="sxs-lookup"><span data-stu-id="a0a72-143">A collection of status codes.</span></span> <span data-ttu-id="a0a72-144">Wywołanie gRPC, które zakończy się niepowodzeniem ze zgodnym stanem, zostanie automatycznie ponowione.</span><span class="sxs-lookup"><span data-stu-id="a0a72-144">A gRPC call that fails with a matching status will be automatically retried.</span></span> <span data-ttu-id="a0a72-145">Aby uzyskać więcej informacji na temat kodów stanu, zobacz [kody stanu i ich użycie w programie gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span><span class="sxs-lookup"><span data-stu-id="a0a72-145">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> <span data-ttu-id="a0a72-146">Wymagany jest co najmniej jeden kod stanu z możliwością ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="a0a72-146">At least one retryable status code is required.</span></span> |

## <a name="hedging"></a><span data-ttu-id="a0a72-147">Zabezpieczanie</span><span class="sxs-lookup"><span data-stu-id="a0a72-147">Hedging</span></span>

<span data-ttu-id="a0a72-148">Zabezpieczanie jest alternatywną strategią ponawiania.</span><span class="sxs-lookup"><span data-stu-id="a0a72-148">Hedging is an alternative retry strategy.</span></span> <span data-ttu-id="a0a72-149">Zabezpieczenia żywopłotu umożliwiają agresywne wysyłanie wielu kopii pojedynczego wywołania gRPC bez oczekiwania na odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="a0a72-149">Hedging enables aggressively sending multiple copies of a single gRPC call without waiting for a response.</span></span> <span data-ttu-id="a0a72-150">Wywołania gRPC hedgingu mogą być wykonywane wiele razy na serwerze i jest używany pierwszy udany wynik.</span><span class="sxs-lookup"><span data-stu-id="a0a72-150">Hedged gRPC calls may be executed multiple times on the server and the first successful result is used.</span></span> <span data-ttu-id="a0a72-151">Należy pamiętać, że Zabezpieczanie jest włączone tylko dla metod, które są bezpieczne do wykonywania wiele razy bez niekorzystnego skutku.</span><span class="sxs-lookup"><span data-stu-id="a0a72-151">It's important that hedging is only enabled for methods that are safe to execute multiple times without adverse effect.</span></span>

<span data-ttu-id="a0a72-152">Żywopłot ma wady i zalety w porównaniu do ponownych prób:</span><span class="sxs-lookup"><span data-stu-id="a0a72-152">Hedging has pros and cons when compared to retries:</span></span> 

* <span data-ttu-id="a0a72-153">Zalety zabezpieczenia przed żywopłotem to pomyślne zwrócenie wyników.</span><span class="sxs-lookup"><span data-stu-id="a0a72-153">An advantage to hedging is it might return a successful result faster.</span></span> <span data-ttu-id="a0a72-154">Umożliwia wiele jednocześnie gRPC wywołań i zakończy się, gdy pierwszy udany wynik jest dostępny.</span><span class="sxs-lookup"><span data-stu-id="a0a72-154">It allows for multiple simultaneously gRPC calls and will complete when the first successful result is available.</span></span> 
* <span data-ttu-id="a0a72-155">Wadą żywopłotu jest wasteful.</span><span class="sxs-lookup"><span data-stu-id="a0a72-155">A disadvantage to hedging is it can be wasteful.</span></span> <span data-ttu-id="a0a72-156">Wiele wywołań może zostać wykonanych i wszystkie powiodły się.</span><span class="sxs-lookup"><span data-stu-id="a0a72-156">Multiple calls could be made and all succeed.</span></span> <span data-ttu-id="a0a72-157">Tylko pierwszy wynik jest używany, a pozostałe są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="a0a72-157">Only the first result is used and the rest are discarded.</span></span>

## <a name="configure-a-grpc-hedging-policy"></a><span data-ttu-id="a0a72-158">Konfigurowanie zasad żywopłotu gRPC</span><span class="sxs-lookup"><span data-stu-id="a0a72-158">Configure a gRPC hedging policy</span></span>

<span data-ttu-id="a0a72-159">Zasady zabezpieczania są konfigurowane jak zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="a0a72-159">A hedging policy is configured like a retry policy.</span></span> <span data-ttu-id="a0a72-160">Należy pamiętać, że nie można łączyć zasad zabezpieczania z zasadami ponawiania.</span><span class="sxs-lookup"><span data-stu-id="a0a72-160">Note that a hedging policy can't be combined with a retry policy.</span></span>

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    HedgingPolicy = new HedgingPolicy
    {
        MaxAttempts = 5,
        NonFatalStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

### <a name="grpc-hedging-options"></a><span data-ttu-id="a0a72-161">Opcje żywopłotu gRPC</span><span class="sxs-lookup"><span data-stu-id="a0a72-161">gRPC hedging options</span></span>

<span data-ttu-id="a0a72-162">W poniższej tabeli opisano opcje konfigurowania zasad zabezpieczania gRPC:</span><span class="sxs-lookup"><span data-stu-id="a0a72-162">The following table describes options for configuring gRPC hedging policies:</span></span>

| <span data-ttu-id="a0a72-163">Opcja</span><span class="sxs-lookup"><span data-stu-id="a0a72-163">Option</span></span> | <span data-ttu-id="a0a72-164">Opis</span><span class="sxs-lookup"><span data-stu-id="a0a72-164">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="a0a72-165">Zasady zabezpieczania będą wysyłać do tej liczby wywołań.</span><span class="sxs-lookup"><span data-stu-id="a0a72-165">The hedging policy will send up to this number of calls.</span></span> <span data-ttu-id="a0a72-166">`MaxAttempts` przedstawia łączną liczbę wszystkich prób, łącznie z pierwotną próbą.</span><span class="sxs-lookup"><span data-stu-id="a0a72-166">`MaxAttempts` represents the total number of all attempts, including the original attempt.</span></span> <span data-ttu-id="a0a72-167">Ta wartość jest ograniczona, ponieważ `GrpcChannelOptions.MaxRetryAttempts` wartości domyślne to 5.</span><span class="sxs-lookup"><span data-stu-id="a0a72-167">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="a0a72-168">Wartość jest wymagana i musi być równa 2 lub większa.</span><span class="sxs-lookup"><span data-stu-id="a0a72-168">A value is required and must be 2 or greater.</span></span> |
| `HedgingDelay` | <span data-ttu-id="a0a72-169">Pierwsze wywołanie zostanie wysłane natychmiast, ale kolejne wywołania zabezpieczające zostaną opóźnione o tę wartość.</span><span class="sxs-lookup"><span data-stu-id="a0a72-169">The first call will be sent immediately, but the subsequent hedging calls will be delayed by this value.</span></span> <span data-ttu-id="a0a72-170">Gdy opóźnienie jest ustawione na zero lub `null` wszystkie wywołania zabezpieczony są wysyłane natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="a0a72-170">When the delay is set to zero or `null`, all hedged calls are sent immediately.</span></span> <span data-ttu-id="a0a72-171">Wartość domyślna to zero.</span><span class="sxs-lookup"><span data-stu-id="a0a72-171">Default value is zero.</span></span> |
| `NonFatalStatusCodes` | <span data-ttu-id="a0a72-172">Kolekcja kodów stanu wskazująca inne wywołania żywopłotu może nadal się powieść.</span><span class="sxs-lookup"><span data-stu-id="a0a72-172">A collection of status codes which indicate other hedge calls may still succeed.</span></span> <span data-ttu-id="a0a72-173">Jeśli serwer zwrócił kod stanu niekrytycznego, nastąpi kontynuowanie wywołań zabezpieczonych.</span><span class="sxs-lookup"><span data-stu-id="a0a72-173">If a non-fatal status code is returned by the server, hedged calls will continue.</span></span> <span data-ttu-id="a0a72-174">W przeciwnym razie oczekujące żądania zostaną anulowane i zostanie zwrócony błąd do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a0a72-174">Otherwise, outstanding requests will be canceled and the error returned to the app.</span></span> <span data-ttu-id="a0a72-175">Aby uzyskać więcej informacji na temat kodów stanu, zobacz [kody stanu i ich użycie w programie gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span><span class="sxs-lookup"><span data-stu-id="a0a72-175">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="a0a72-176">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a0a72-176">Additional resources</span></span>

* <xref:grpc/client>
* [<span data-ttu-id="a0a72-177">Ponowienie ogólnych wskazówek — najlepsze rozwiązania dotyczące aplikacji w chmurze</span><span class="sxs-lookup"><span data-stu-id="a0a72-177">Retry general guidance - Best practices for cloud applications</span></span>](/azure/architecture/best-practices/transient-faults)
