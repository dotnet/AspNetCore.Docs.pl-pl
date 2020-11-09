---
title: Wywoływanie usług gRPC przy użyciu klienta platformy .NET
author: jamesnk
description: Dowiedz się, jak wywoływać usługi gRPC Services za pomocą programu .NET gRPC Client.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/client
ms.openlocfilehash: 9322020083ce25b00b2979633ae8a692cfd4da4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060966"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="a7b48-103">Wywoływanie usług gRPC przy użyciu klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="a7b48-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="a7b48-104">Biblioteka klienta .NET gRPC jest dostępna w pakiecie NuGet [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="a7b48-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="a7b48-105">W tym dokumencie wyjaśniono, jak:</span><span class="sxs-lookup"><span data-stu-id="a7b48-105">This document explains how to:</span></span>

* <span data-ttu-id="a7b48-106">Skonfiguruj klienta gRPC do wywoływania usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7b48-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="a7b48-107">GRPC wywołania jednoargumentowe, przesyłania strumieniowego serwera, przesyłania strumieniowego klienta i dwukierunkowych metod przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="a7b48-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="a7b48-108">Konfigurowanie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="a7b48-108">Configure gRPC client</span></span>

<span data-ttu-id="a7b48-109">gRPC klienci są konkretnymi typami klientów, które są [generowane z plików *\* . proto*](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="a7b48-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="a7b48-110">Konkretny klient gRPC ma metody, które są tłumaczone na usługę gRPC w pliku *\* . proto* .</span><span class="sxs-lookup"><span data-stu-id="a7b48-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="a7b48-111">Na przykład wywołana usługa `Greeter` generuje `GreeterClient` Typ z metodami wywołania usługi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="a7b48-112">Klient gRPC jest tworzony na podstawie kanału.</span><span class="sxs-lookup"><span data-stu-id="a7b48-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="a7b48-113">Uruchom `GrpcChannel.ForAddress` polecenie, aby utworzyć kanał, a następnie użyj kanału, aby utworzyć klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="a7b48-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="a7b48-114">Kanał reprezentuje długotrwałe połączenie z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7b48-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="a7b48-115">Po utworzeniu kanału jest on konfigurowany z opcjami związanymi z wywoływaniem usługi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-115">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="a7b48-116">Na przykład `HttpClient` używane do wykonywania wywołań, maksymalnego rozmiaru wysyłania i odbierania wiadomości oraz rejestrowania można określić `GrpcChannelOptions` i użyć z `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="a7b48-117">Aby uzyskać pełną listę opcji, zobacz [Opcje konfiguracji klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="a7b48-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="a7b48-118">Konfigurowanie protokołu TLS</span><span class="sxs-lookup"><span data-stu-id="a7b48-118">Configure TLS</span></span>

<span data-ttu-id="a7b48-119">Klient gRPC musi używać tego samego zabezpieczenia na poziomie połączenia co nazwana usługa.</span><span class="sxs-lookup"><span data-stu-id="a7b48-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="a7b48-120">gRPC Client Transport Layer Security (TLS) jest konfigurowany podczas tworzenia kanału gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7b48-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="a7b48-121">Klient gRPC zgłasza błąd podczas wywoływania usługi, a zabezpieczenia na poziomie połączenia kanału i usługi nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="a7b48-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="a7b48-122">Aby skonfigurować kanał gRPC do korzystania z protokołu TLS, upewnij się, że adres serwera zaczyna się od `https` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="a7b48-123">Na przykład `GrpcChannel.ForAddress("https://localhost:5001")` używa protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a7b48-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="a7b48-124">Kanał gRPC automatycznie negocjuje połączenie zabezpieczone przez protokół TLS i używa bezpiecznego połączenia do wykonywania wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7b48-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="a7b48-125">gRPC obsługuje uwierzytelnianie certyfikatu klienta za pośrednictwem protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="a7b48-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="a7b48-126">Informacje dotyczące konfigurowania certyfikatów klienta przy użyciu kanału gRPC można znaleźć w temacie <xref:grpc/authn-and-authz#client-certificate-authentication> .</span><span class="sxs-lookup"><span data-stu-id="a7b48-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="a7b48-127">Aby wywoływać niezabezpieczone usługi gRPC, upewnij się, że adres serwera zaczyna się od `http` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="a7b48-128">Na przykład `GrpcChannel.ForAddress("http://localhost:5000")` używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a7b48-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="a7b48-129">W przypadku platformy .NET Core 3,1 lub nowszej dodatkowa konfiguracja jest wymagana do [wywołania niezabezpieczonych usług gRPC z klientem platformy .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="a7b48-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="a7b48-130">Wydajność klienta</span><span class="sxs-lookup"><span data-stu-id="a7b48-130">Client performance</span></span>

<span data-ttu-id="a7b48-131">Wydajność i użycie kanału i klienta:</span><span class="sxs-lookup"><span data-stu-id="a7b48-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="a7b48-132">Tworzenie kanału może być kosztowną operacją.</span><span class="sxs-lookup"><span data-stu-id="a7b48-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="a7b48-133">Użycie kanału dla wywołań gRPC zapewnia korzyści wynikające z wydajności.</span><span class="sxs-lookup"><span data-stu-id="a7b48-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="a7b48-134">gRPC klienci są tworzone za pomocą kanałów.</span><span class="sxs-lookup"><span data-stu-id="a7b48-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="a7b48-135">gRPC klienci są obiektami lekkimi i nie muszą być buforowane ani ponownie używane.</span><span class="sxs-lookup"><span data-stu-id="a7b48-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="a7b48-136">Wielu klientów gRPC można utworzyć na podstawie kanału, w tym różnych typów klientów.</span><span class="sxs-lookup"><span data-stu-id="a7b48-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="a7b48-137">Kanał i klienci utworzeni z kanału mogą być bezpiecznie używani przez wiele wątków.</span><span class="sxs-lookup"><span data-stu-id="a7b48-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="a7b48-138">Klienci utworzeni z kanału mogą wykonywać wiele jednoczesnych wywołań.</span><span class="sxs-lookup"><span data-stu-id="a7b48-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="a7b48-139">`GrpcChannel.ForAddress` nie jest jedyną opcją tworzenia klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7b48-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="a7b48-140">W przypadku wywoływania usług gRPC z poziomu aplikacji ASP.NET Core należy wziąć pod uwagę [integrację klienta gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="a7b48-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="a7b48-141">Integracja gRPC z programem `HttpClientFactory` oferuje scentralizowaną alternatywę do tworzenia klientów gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7b48-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="a7b48-142">Wywoływanie gRPC za pośrednictwem protokołu HTTP/2 z `Grpc.Net.Client` nie jest obecnie obsługiwane w oprogramowaniu Xamarin.</span><span class="sxs-lookup"><span data-stu-id="a7b48-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="a7b48-143">Pracujemy nad ulepszeniem obsługi protokołu HTTP/2 w przyszłej wersji platformy Xamarin.</span><span class="sxs-lookup"><span data-stu-id="a7b48-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="a7b48-144">[GRPC. Core](https://www.nuget.org/packages/Grpc.Core) i [GRPC-Web](xref:grpc/browser) to żywotne alternatywy, które działają dzisiaj.</span><span class="sxs-lookup"><span data-stu-id="a7b48-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="a7b48-145">Utwórz wywołania gRPC</span><span class="sxs-lookup"><span data-stu-id="a7b48-145">Make gRPC calls</span></span>

<span data-ttu-id="a7b48-146">Wywołanie gRPC jest inicjowane przez wywołanie metody na kliencie.</span><span class="sxs-lookup"><span data-stu-id="a7b48-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="a7b48-147">Klient gRPC będzie obsługiwał serializację komunikatów i odnoszący się do wywołania gRPC do prawidłowej usługi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="a7b48-148">gRPC ma różne typy metod.</span><span class="sxs-lookup"><span data-stu-id="a7b48-148">gRPC has different types of methods.</span></span> <span data-ttu-id="a7b48-149">Sposób, w jaki klient jest używany do wykonywania wywołania gRPC, zależy od typu metody o nazwie.</span><span class="sxs-lookup"><span data-stu-id="a7b48-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="a7b48-150">Typy metod gRPC są następujące:</span><span class="sxs-lookup"><span data-stu-id="a7b48-150">The gRPC method types are:</span></span>

* <span data-ttu-id="a7b48-151">Jednoargumentowy</span><span class="sxs-lookup"><span data-stu-id="a7b48-151">Unary</span></span>
* <span data-ttu-id="a7b48-152">Przesyłanie strumieniowe serwera</span><span class="sxs-lookup"><span data-stu-id="a7b48-152">Server streaming</span></span>
* <span data-ttu-id="a7b48-153">Przesyłanie strumieniowe klienta</span><span class="sxs-lookup"><span data-stu-id="a7b48-153">Client streaming</span></span>
* <span data-ttu-id="a7b48-154">Dwukierunkowe przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="a7b48-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="a7b48-155">Wywołanie jednoargumentowe</span><span class="sxs-lookup"><span data-stu-id="a7b48-155">Unary call</span></span>

<span data-ttu-id="a7b48-156">Wywołanie jednoargumentowe rozpoczyna się od klienta wysyłającego komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="a7b48-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="a7b48-157">Komunikat odpowiedzi jest zwracany po zakończeniu działania usługi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="a7b48-158">Każda Jednoargumentowa metoda usługi w pliku *\* . proto* powoduje dwie metody .NET na konkretnym typie klienta gRPC do wywoływania metody: Metoda asynchroniczna i Metoda blokująca.</span><span class="sxs-lookup"><span data-stu-id="a7b48-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="a7b48-159">Na przykład istnieją `GreeterClient` dwa sposoby wywoływania `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="a7b48-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="a7b48-160">`GreeterClient.SayHelloAsync` -wywołuje `Greeter.SayHello` usługę asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="a7b48-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="a7b48-161">Można oczekiwać.</span><span class="sxs-lookup"><span data-stu-id="a7b48-161">Can be awaited.</span></span>
* <span data-ttu-id="a7b48-162">`GreeterClient.SayHello` -wywołuje `Greeter.SayHello` usługę i bloki do momentu ukończenia.</span><span class="sxs-lookup"><span data-stu-id="a7b48-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="a7b48-163">Nie używaj w kodzie asynchronicznym.</span><span class="sxs-lookup"><span data-stu-id="a7b48-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="a7b48-164">Wywołanie przesyłania strumieniowego serwera</span><span class="sxs-lookup"><span data-stu-id="a7b48-164">Server streaming call</span></span>

<span data-ttu-id="a7b48-165">Wywołanie przesyłania strumieniowego serwera rozpoczyna się od klienta wysyłającego komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="a7b48-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="a7b48-166">`ResponseStream.MoveNext()` odczytuje komunikaty przesyłane strumieniowo z usługi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="a7b48-167">Wywołanie przesyłania strumieniowego serwera jest kompletne, gdy `ResponseStream.MoveNext()` zwraca `false` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="a7b48-168">W przypadku korzystania z języka C# 8 lub nowszego `await foreach` składnia może być używana do odczytywania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="a7b48-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="a7b48-169">`IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozszerzająca odczytuje wszystkie komunikaty ze strumienia odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="a7b48-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="a7b48-170">Wywołanie przesyłania strumieniowego klienta</span><span class="sxs-lookup"><span data-stu-id="a7b48-170">Client streaming call</span></span>

<span data-ttu-id="a7b48-171">Wywołanie przesyłania strumieniowego klienta rozpoczyna się *bez* wysyłania komunikatu przez klienta.</span><span class="sxs-lookup"><span data-stu-id="a7b48-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a7b48-172">Klient może wysyłać wiadomości za pomocą polecenia `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a7b48-173">Gdy klient zakończył wysyłanie komunikatów, `RequestStream.CompleteAsync()` powinien zostać wywołany w celu powiadomienia usługi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="a7b48-174">Wywołanie jest zakończone, gdy usługa zwróci komunikat odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-174">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="a7b48-175">Dwukierunkowe wywołanie przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="a7b48-175">Bi-directional streaming call</span></span>

<span data-ttu-id="a7b48-176">Dwukierunkowe wywołanie przesyłania strumieniowego rozpoczyna się *bez* wysyłania komunikatu przez klienta.</span><span class="sxs-lookup"><span data-stu-id="a7b48-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a7b48-177">Klient może wysyłać wiadomości za pomocą polecenia `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a7b48-178">Komunikaty przesyłane strumieniowo z usługi są dostępne z `ResponseStream.MoveNext()` lub `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="a7b48-179">Dwukierunkowe wywołanie przesyłania strumieniowego jest kompletne, gdy `ResponseStream` nie ma więcej komunikatów.</span><span class="sxs-lookup"><span data-stu-id="a7b48-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="a7b48-180">Aby uzyskać najlepszą wydajność i uniknąć niepotrzebnych błędów w kliencie i usłudze, spróbuj bezpiecznie wykonać wywołania przesyłania strumieniowego dwukierunkowe.</span><span class="sxs-lookup"><span data-stu-id="a7b48-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="a7b48-181">Wywołanie dwukierunkowe kończy się bezpiecznie, gdy serwer zakończył odczytywanie strumienia żądań, a klient zakończył odczytywanie strumienia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="a7b48-182">Poprzednie przykładowe wywołanie to jeden przykład dwukierunkowego wywołania, które się zakończyło.</span><span class="sxs-lookup"><span data-stu-id="a7b48-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="a7b48-183">W wywołaniu klient:</span><span class="sxs-lookup"><span data-stu-id="a7b48-183">In the call, the client:</span></span>

1. <span data-ttu-id="a7b48-184">Uruchamia nowe dwukierunkowe wywołanie przesyłania strumieniowego przez wywołanie `EchoClient.Echo` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="a7b48-185">Tworzy zadanie w tle do odczytywania wiadomości z usługi przy użyciu programu `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="a7b48-186">Wysyła komunikaty do serwera przy użyciu programu `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="a7b48-187">Powiadamia serwer, który zakończył wysyłanie komunikatów z programu `RequestStream.CompleteAsync()` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="a7b48-188">Czeka, aż zadanie w tle odczytaje wszystkie wiadomości przychodzące.</span><span class="sxs-lookup"><span data-stu-id="a7b48-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="a7b48-189">W trakcie dwukierunkowego wywołania przesyłania strumieniowego klient i usługa mogą w dowolnym momencie wysyłać komunikaty do siebie.</span><span class="sxs-lookup"><span data-stu-id="a7b48-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="a7b48-190">Najlepsza logika klienta do współpracy z dwukierunkowym wywołaniem różni się w zależności od logiki usługi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="a7b48-191">Dostęp do przyczep gRPC</span><span class="sxs-lookup"><span data-stu-id="a7b48-191">Access gRPC trailers</span></span>

<span data-ttu-id="a7b48-192">wywołania gRPC mogą zwracać gRPC przyczep.</span><span class="sxs-lookup"><span data-stu-id="a7b48-192">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="a7b48-193">przyczepy gRPC są używane do dostarczania metadanych nazw/wartości dotyczących wywołania.</span><span class="sxs-lookup"><span data-stu-id="a7b48-193">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="a7b48-194">Przyczepy zapewniają podobną funkcjonalność do nagłówków HTTP, ale są odbierane na końcu wywołania.</span><span class="sxs-lookup"><span data-stu-id="a7b48-194">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="a7b48-195">gRPC przyczep są dostępne przy użyciu `GetTrailers()` , która zwraca kolekcję metadanych.</span><span class="sxs-lookup"><span data-stu-id="a7b48-195">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="a7b48-196">Po zakończeniu odpowiedzi są zwracane przyczepy. w związku z tym przed uzyskaniem dostępu do przyczep należy oczekiwać wszystkich komunikatów odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a7b48-196">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="a7b48-197">Wywołania jednoargumentowe i wywołanie przesyłania strumieniowego klienta muszą oczekiwać `ResponseAsync` przed wywołaniem `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="a7b48-197">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="a7b48-198">Wywołania przesyłania strumieniowego serwera i dwukierunkowe muszą kończyć się oczekiwaniem przed wywołaniem strumienia odpowiedzi `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="a7b48-198">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="a7b48-199">gRPC przyczepy są również dostępne z programu `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="a7b48-199">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="a7b48-200">Usługa może zwracać przyczepy ze stanem gRPC innym niż OK.</span><span class="sxs-lookup"><span data-stu-id="a7b48-200">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="a7b48-201">W tej sytuacji przyczepy są pobierane z wyjątku zgłoszonego przez klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="a7b48-201">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a><span data-ttu-id="a7b48-202">Konfigurowanie terminu ostatecznego</span><span class="sxs-lookup"><span data-stu-id="a7b48-202">Configure deadline</span></span>

<span data-ttu-id="a7b48-203">Zaleca się skonfigurowanie ostatecznego terminu wywołania gRPC, ponieważ zawiera on górny limit czasu, w którym można uruchomić wywołanie.</span><span class="sxs-lookup"><span data-stu-id="a7b48-203">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="a7b48-204">Usługa błędna uniemożliwia uruchamianie nieograniczonego i wyczerpania zasobów serwera.</span><span class="sxs-lookup"><span data-stu-id="a7b48-204">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="a7b48-205">Terminy są przydatnym narzędziem do tworzenia niezawodnych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7b48-205">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="a7b48-206">Skonfiguruj, `CallOptions.Deadline` Aby ustawić ostateczny termin wywołania gRPC:</span><span class="sxs-lookup"><span data-stu-id="a7b48-206">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="a7b48-207">Aby uzyskać więcej informacji, zobacz <xref:grpc/deadlines-cancellation#deadlines>.</span><span class="sxs-lookup"><span data-stu-id="a7b48-207">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7b48-208">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a7b48-208">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
