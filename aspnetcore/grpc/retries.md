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
# <a name="transient-fault-handling-with-grpc-retries"></a>Przejściowa obsługa błędów z ponownymi próbami gRPC

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

gRPC ponownych prób to funkcja, która umożliwia klientom gRPC automatyczne ponawianie nieudanych wywołań. W tym artykule opisano, jak skonfigurować zasady ponawiania w celu odporności, odpornych na uszkodzenia aplikacji gRPC na platformie .NET.

gRPC ponownych prób wymaga [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) w wersji 2.36.0-pre1 lub nowszej.

## <a name="transient-fault-handling"></a>Obsługa błędu przejściowego

wywołania gRPC mogą być przerywane przez błędy przejściowe. Błędy przejściowe obejmują:

* Chwilowa utrata łączności sieciowej.
* Tymczasowa niedostępność usługi.
* Limity czasu z powodu obciążenia serwera.

Gdy wywołanie gRPC zostanie przerwane, klient zgłosi `RpcException` ze szczegółowymi informacjami o błędzie. Aplikacja kliencka musi przechwycić wyjątek i wybrać sposób obsługi błędu.

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

Duplikowanie logiki ponawiania prób w całej aplikacji jest pełne i podatne na błędy. Na szczęście klient .NET gRPC ma wbudowaną obsługę automatycznych ponownych prób.

## <a name="configure-a-grpc-retry-policy"></a>Skonfiguruj zasady ponawiania gRPC

Zasady ponawiania są konfigurowane po utworzeniu kanału gRPC:

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

Powyższy kod ma następujące działanie:

* Tworzy `MethodConfig` . Zasady ponawiania można skonfigurować dla metody i metody są dopasowywane przy użyciu `Names` właściwości. Ta metoda jest konfigurowana z `MethodName.Default` , dlatego jest stosowana do wszystkich metod gRPC wywoływanych przez ten kanał.
* Konfiguruje zasady ponawiania. Te zasady instruują klientów do automatycznego ponawiania prób gRPC wywołań zakończonych niepowodzeniem z kodem stanu `Unavailable` .
* Konfiguruje utworzony kanał, aby używać zasad ponawiania próby przez ustawienie `GrpcChannelOptions.ServiceConfig` .

Klienci gRPC utworzeni przy użyciu kanału zostaną automatycznie ponowiać nieudane wywołania:

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a>Opcje ponowień gRPC

W poniższej tabeli opisano opcje konfigurowania zasad ponawiania gRPC:

| Opcja | Opis |
| ------ | ----------- |
| `MaxAttempts` | Maksymalna liczba prób wywołania, łącznie z pierwotną próbą. Ta wartość jest ograniczona, ponieważ `GrpcChannelOptions.MaxRetryAttempts` wartości domyślne to 5. Wartość jest wymagana i musi być większa niż 1. |
| `InitialBackoff` | Początkowe opóźnienie wycofywania między ponownymi próbami. Losowe opóźnienie między 0 a bieżącym wycofywania określa, kiedy zostanie podjęta kolejna próba ponowienia. Po każdej próbie bieżąca wycofywania zostanie pomnożona przez `BackoffMultiplier` . Wartość jest wymagana i musi być większa od zera. |
| `MaxBackoff` | Wartość maksymalna wycofywania umieszcza górny limit na wartości wykładniczej wycofywania wzrostu. Wartość jest wymagana i musi być większa od zera. |
| `BackoffMultiplier` | Wycofywania zostanie pomnożona przez tę wartość po każdej próbie ponowienia i zwiększy się wykładniczo, gdy mnożnik jest większy niż 1. Wartość jest wymagana i musi być większa od zera. |
| `RetryableStatusCodes` | Kolekcja kodów stanu. Wywołanie gRPC, które zakończy się niepowodzeniem ze zgodnym stanem, zostanie automatycznie ponowione. Aby uzyskać więcej informacji na temat kodów stanu, zobacz [kody stanu i ich użycie w programie gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html). Wymagany jest co najmniej jeden kod stanu z możliwością ponowienia próby. |

## <a name="hedging"></a>Zabezpieczanie

Zabezpieczanie jest alternatywną strategią ponawiania. Zabezpieczenia żywopłotu umożliwiają agresywne wysyłanie wielu kopii pojedynczego wywołania gRPC bez oczekiwania na odpowiedź. Wywołania gRPC hedgingu mogą być wykonywane wiele razy na serwerze i jest używany pierwszy udany wynik. Należy pamiętać, że Zabezpieczanie jest włączone tylko dla metod, które są bezpieczne do wykonywania wiele razy bez niekorzystnego skutku.

Żywopłot ma wady i zalety w porównaniu do ponownych prób: 

* Zalety zabezpieczenia przed żywopłotem to pomyślne zwrócenie wyników. Umożliwia wiele jednocześnie gRPC wywołań i zakończy się, gdy pierwszy udany wynik jest dostępny. 
* Wadą żywopłotu jest wasteful. Wiele wywołań może zostać wykonanych i wszystkie powiodły się. Tylko pierwszy wynik jest używany, a pozostałe są odrzucane.

## <a name="configure-a-grpc-hedging-policy"></a>Konfigurowanie zasad żywopłotu gRPC

Zasady zabezpieczania są konfigurowane jak zasady ponawiania. Należy pamiętać, że nie można łączyć zasad zabezpieczania z zasadami ponawiania.

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

### <a name="grpc-hedging-options"></a>Opcje żywopłotu gRPC

W poniższej tabeli opisano opcje konfigurowania zasad zabezpieczania gRPC:

| Opcja | Opis |
| ------ | ----------- |
| `MaxAttempts` | Zasady zabezpieczania będą wysyłać do tej liczby wywołań. `MaxAttempts` przedstawia łączną liczbę wszystkich prób, łącznie z pierwotną próbą. Ta wartość jest ograniczona, ponieważ `GrpcChannelOptions.MaxRetryAttempts` wartości domyślne to 5. Wartość jest wymagana i musi być równa 2 lub większa. |
| `HedgingDelay` | Pierwsze wywołanie zostanie wysłane natychmiast, ale kolejne wywołania zabezpieczające zostaną opóźnione o tę wartość. Gdy opóźnienie jest ustawione na zero lub `null` wszystkie wywołania zabezpieczony są wysyłane natychmiastowo. Wartość domyślna to zero. |
| `NonFatalStatusCodes` | Kolekcja kodów stanu wskazująca inne wywołania żywopłotu może nadal się powieść. Jeśli serwer zwrócił kod stanu niekrytycznego, nastąpi kontynuowanie wywołań zabezpieczonych. W przeciwnym razie oczekujące żądania zostaną anulowane i zostanie zwrócony błąd do aplikacji. Aby uzyskać więcej informacji na temat kodów stanu, zobacz [kody stanu i ich użycie w programie gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html). |

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/client>
* [Ponowienie ogólnych wskazówek — najlepsze rozwiązania dotyczące aplikacji w chmurze](/azure/architecture/best-practices/transient-faults)
