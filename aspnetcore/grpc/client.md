---
title: Wywoływanie usług gRPC przy użyciu klienta platformy .NET
author: jamesnk
description: Dowiedz się, jak wywoływać usługi gRPC Services za pomocą programu .NET gRPC Client.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: 65e386298af26d36900f13a5eb11aab7c012c2b0
ms.sourcegitcommit: 756c78f6dbfa77c5d718969cdce20639b8ca0a17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515612"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="112e3-103">Wywoływanie usług gRPC przy użyciu klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="112e3-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="112e3-104">Biblioteka klienta .NET gRPC jest dostępna w pakiecie NuGet [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="112e3-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="112e3-105">W tym dokumencie wyjaśniono, jak:</span><span class="sxs-lookup"><span data-stu-id="112e3-105">This document explains how to:</span></span>

* <span data-ttu-id="112e3-106">Skonfiguruj klienta gRPC do wywoływania usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="112e3-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="112e3-107">GRPC wywołania jednoargumentowe, przesyłania strumieniowego serwera, przesyłania strumieniowego klienta i dwukierunkowych metod przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="112e3-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="112e3-108">Konfigurowanie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="112e3-108">Configure gRPC client</span></span>

<span data-ttu-id="112e3-109">gRPC klienci są konkretnymi typami klientów, które są [generowane z plików \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="112e3-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="112e3-110">Konkretny klient gRPC ma metody, które są tłumaczone na usługę gRPC w pliku \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="112e3-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="112e3-111">Klient gRPC jest tworzony na podstawie kanału.</span><span class="sxs-lookup"><span data-stu-id="112e3-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="112e3-112">Uruchom `GrpcChannel.ForAddress` polecenie, aby utworzyć kanał, a następnie użyj kanału, aby utworzyć klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="112e3-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="112e3-113">Kanał reprezentuje długotrwałe połączenie z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="112e3-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="112e3-114">Po utworzeniu kanału jest on konfigurowany z opcjami związanymi z wywoływaniem usługi.</span><span class="sxs-lookup"><span data-stu-id="112e3-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="112e3-115">Na przykład `HttpClient` używane do wykonywania wywołań, maksymalnego rozmiaru wysyłania i odbierania wiadomości oraz rejestrowania można określić `GrpcChannelOptions` i użyć z `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="112e3-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="112e3-116">Aby uzyskać pełną listę opcji, zobacz [Opcje konfiguracji klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="112e3-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="112e3-117">Konfigurowanie protokołu TLS</span><span class="sxs-lookup"><span data-stu-id="112e3-117">Configure TLS</span></span>

<span data-ttu-id="112e3-118">Klient gRPC musi używać tego samego zabezpieczenia na poziomie połączenia co nazwana usługa.</span><span class="sxs-lookup"><span data-stu-id="112e3-118">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="112e3-119">gRPC Client Transport Layer Security (TLS) jest konfigurowany podczas tworzenia kanału gRPC.</span><span class="sxs-lookup"><span data-stu-id="112e3-119">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="112e3-120">Klient gRPC zgłasza błąd podczas wywoływania usługi, a zabezpieczenia na poziomie połączenia kanału i usługi nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="112e3-120">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="112e3-121">Aby skonfigurować kanał gRPC do korzystania z protokołu TLS, upewnij się, że adres serwera zaczyna się od `https` .</span><span class="sxs-lookup"><span data-stu-id="112e3-121">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="112e3-122">Na przykład `GrpcChannel.ForAddress("https://localhost:5001")` używa protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="112e3-122">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="112e3-123">Kanał gRPC automatycznie negotates połączenie zabezpieczone przez protokół TLS i używa bezpiecznego połączenia do wykonywania wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="112e3-123">The gRPC channel automatically negotates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="112e3-124">gRPC obsługuje uwierzytelnianie certyfikatu klienta za pośrednictwem protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="112e3-124">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="112e3-125">Informacje dotyczące konfigurowania certyfikatów klienta przy użyciu kanału gRPC można znaleźć w temacie <xref:grpc/authn-and-authz#client-certificate-authentication> .</span><span class="sxs-lookup"><span data-stu-id="112e3-125">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="112e3-126">Aby wywoływać niezabezpieczone usługi gRPC, upewnij się, że adres serwera zaczyna się od `http` .</span><span class="sxs-lookup"><span data-stu-id="112e3-126">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="112e3-127">Na przykład `GrpcChannel.ForAddress("http://localhost:5000")` używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="112e3-127">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="112e3-128">W przypadku platformy .NET Core 3,1 lub nowszej dodatkowa konfiguracja jest wymagana do [wywołania niezabezpieczonych usług gRPC z klientem platformy .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="112e3-128">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="112e3-129">Wydajność klienta</span><span class="sxs-lookup"><span data-stu-id="112e3-129">Client performance</span></span>

<span data-ttu-id="112e3-130">Wydajność i użycie kanału i klienta:</span><span class="sxs-lookup"><span data-stu-id="112e3-130">Channel and client performance and usage:</span></span>

* <span data-ttu-id="112e3-131">Tworzenie kanału może być kosztowną operacją.</span><span class="sxs-lookup"><span data-stu-id="112e3-131">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="112e3-132">Użycie kanału dla wywołań gRPC zapewnia korzyści wynikające z wydajności.</span><span class="sxs-lookup"><span data-stu-id="112e3-132">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="112e3-133">gRPC klienci są tworzone za pomocą kanałów.</span><span class="sxs-lookup"><span data-stu-id="112e3-133">gRPC clients are created with channels.</span></span> <span data-ttu-id="112e3-134">gRPC klienci są obiektami lekkimi i nie muszą być buforowane ani ponownie używane.</span><span class="sxs-lookup"><span data-stu-id="112e3-134">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="112e3-135">Wielu klientów gRPC można utworzyć na podstawie kanału, w tym różnych typów klientów.</span><span class="sxs-lookup"><span data-stu-id="112e3-135">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="112e3-136">Kanał i klienci utworzeni z kanału mogą być bezpiecznie używani przez wiele wątków.</span><span class="sxs-lookup"><span data-stu-id="112e3-136">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="112e3-137">Klienci utworzeni z kanału mogą wykonywać wiele jednoczesnych wywołań.</span><span class="sxs-lookup"><span data-stu-id="112e3-137">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="112e3-138">`GrpcChannel.ForAddress` nie jest jedyną opcją tworzenia klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="112e3-138">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="112e3-139">W przypadku wywoływania usług gRPC z poziomu aplikacji ASP.NET Core należy wziąć pod uwagę [integrację klienta gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="112e3-139">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="112e3-140">Integracja gRPC z programem `HttpClientFactory` oferuje scentralizowaną alternatywę do tworzenia klientów gRPC.</span><span class="sxs-lookup"><span data-stu-id="112e3-140">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="112e3-141">Wywoływanie gRPC za pośrednictwem protokołu HTTP/2 z `Grpc.Net.Client` nie jest obecnie obsługiwane w oprogramowaniu Xamarin.</span><span class="sxs-lookup"><span data-stu-id="112e3-141">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="112e3-142">Pracujemy nad ulepszeniem obsługi protokołu HTTP/2 w przyszłej wersji platformy Xamarin.</span><span class="sxs-lookup"><span data-stu-id="112e3-142">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="112e3-143">[GRPC. Core](https://www.nuget.org/packages/Grpc.Core) i [GRPC-Web](xref:grpc/browser) to żywotne alternatywy, które działają dzisiaj.</span><span class="sxs-lookup"><span data-stu-id="112e3-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="112e3-144">Utwórz wywołania gRPC</span><span class="sxs-lookup"><span data-stu-id="112e3-144">Make gRPC calls</span></span>

<span data-ttu-id="112e3-145">Wywołanie gRPC jest inicjowane przez wywołanie metody na kliencie.</span><span class="sxs-lookup"><span data-stu-id="112e3-145">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="112e3-146">Klient gRPC będzie obsługiwał serializację komunikatów i odnoszący się do wywołania gRPC do prawidłowej usługi.</span><span class="sxs-lookup"><span data-stu-id="112e3-146">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="112e3-147">gRPC ma różne typy metod.</span><span class="sxs-lookup"><span data-stu-id="112e3-147">gRPC has different types of methods.</span></span> <span data-ttu-id="112e3-148">Sposób, w jaki klient jest używany do wykonywania wywołania gRPC, zależy od typu metody o nazwie.</span><span class="sxs-lookup"><span data-stu-id="112e3-148">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="112e3-149">Typy metod gRPC są następujące:</span><span class="sxs-lookup"><span data-stu-id="112e3-149">The gRPC method types are:</span></span>

* <span data-ttu-id="112e3-150">Jednoargumentowy</span><span class="sxs-lookup"><span data-stu-id="112e3-150">Unary</span></span>
* <span data-ttu-id="112e3-151">Przesyłanie strumieniowe serwera</span><span class="sxs-lookup"><span data-stu-id="112e3-151">Server streaming</span></span>
* <span data-ttu-id="112e3-152">Przesyłanie strumieniowe klienta</span><span class="sxs-lookup"><span data-stu-id="112e3-152">Client streaming</span></span>
* <span data-ttu-id="112e3-153">Dwukierunkowe przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="112e3-153">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="112e3-154">Wywołanie jednoargumentowe</span><span class="sxs-lookup"><span data-stu-id="112e3-154">Unary call</span></span>

<span data-ttu-id="112e3-155">Wywołanie jednoargumentowe rozpoczyna się od klienta wysyłającego komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="112e3-155">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="112e3-156">Komunikat odpowiedzi jest zwracany po zakończeniu działania usługi.</span><span class="sxs-lookup"><span data-stu-id="112e3-156">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="112e3-157">Każda Jednoargumentowa metoda usługi w pliku \* \* . proto\* powoduje dwie metody .NET na konkretnym typie klienta gRPC do wywoływania metody: Metoda asynchroniczna i Metoda blokująca.</span><span class="sxs-lookup"><span data-stu-id="112e3-157">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="112e3-158">Na przykład istnieją `GreeterClient` dwa sposoby wywoływania `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="112e3-158">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="112e3-159">`GreeterClient.SayHelloAsync` -wywołuje `Greeter.SayHello` usługę asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="112e3-159">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="112e3-160">Można oczekiwać.</span><span class="sxs-lookup"><span data-stu-id="112e3-160">Can be awaited.</span></span>
* <span data-ttu-id="112e3-161">`GreeterClient.SayHello` -wywołuje `Greeter.SayHello` usługę i bloki do momentu ukończenia.</span><span class="sxs-lookup"><span data-stu-id="112e3-161">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="112e3-162">Nie używaj w kodzie asynchronicznym.</span><span class="sxs-lookup"><span data-stu-id="112e3-162">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="112e3-163">Wywołanie przesyłania strumieniowego serwera</span><span class="sxs-lookup"><span data-stu-id="112e3-163">Server streaming call</span></span>

<span data-ttu-id="112e3-164">Wywołanie przesyłania strumieniowego serwera rozpoczyna się od klienta wysyłającego komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="112e3-164">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="112e3-165">`ResponseStream.MoveNext()` odczytuje komunikaty przesyłane strumieniowo z usługi.</span><span class="sxs-lookup"><span data-stu-id="112e3-165">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="112e3-166">Wywołanie przesyłania strumieniowego serwera jest kompletne, gdy `ResponseStream.MoveNext()` zwraca `false` .</span><span class="sxs-lookup"><span data-stu-id="112e3-166">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="112e3-167">W przypadku korzystania z języka C# 8 lub nowszego `await foreach` składnia może być używana do odczytywania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="112e3-167">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="112e3-168">`IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozszerzająca odczytuje wszystkie komunikaty ze strumienia odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="112e3-168">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="112e3-169">Wywołanie przesyłania strumieniowego klienta</span><span class="sxs-lookup"><span data-stu-id="112e3-169">Client streaming call</span></span>

<span data-ttu-id="112e3-170">Wywołanie przesyłania strumieniowego klienta rozpoczyna się *bez* wysyłania komunikatu przez klienta.</span><span class="sxs-lookup"><span data-stu-id="112e3-170">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="112e3-171">Klient może wysyłać wiadomości za pomocą polecenia `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="112e3-171">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="112e3-172">Gdy klient zakończył wysyłanie komunikatów, `RequestStream.CompleteAsync()` powinien zostać wywołany w celu powiadomienia usługi.</span><span class="sxs-lookup"><span data-stu-id="112e3-172">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="112e3-173">Wywołanie jest zakończone, gdy usługa zwróci komunikat odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="112e3-173">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="112e3-174">Dwukierunkowe wywołanie przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="112e3-174">Bi-directional streaming call</span></span>

<span data-ttu-id="112e3-175">Dwukierunkowe wywołanie przesyłania strumieniowego rozpoczyna się *bez* wysyłania komunikatu przez klienta.</span><span class="sxs-lookup"><span data-stu-id="112e3-175">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="112e3-176">Klient może wysyłać wiadomości za pomocą polecenia `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="112e3-176">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="112e3-177">Komunikaty przesyłane strumieniowo z usługi są dostępne z `ResponseStream.MoveNext()` lub `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="112e3-177">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="112e3-178">Dwukierunkowe wywołanie przesyłania strumieniowego jest kompletne, gdy `ResponseStream` nie ma więcej komunikatów.</span><span class="sxs-lookup"><span data-stu-id="112e3-178">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="112e3-179">Aby uzyskać najlepszą wydajność i uniknąć niepotrzebnych błędów w kliencie i usłudze, spróbuj bezpiecznie wykonać wywołania przesyłania strumieniowego dwukierunkowe.</span><span class="sxs-lookup"><span data-stu-id="112e3-179">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="112e3-180">Wywołanie dwukierunkowe kończy się bezpiecznie, gdy serwer zakończył odczytywanie strumienia żądań, a klient zakończył odczytywanie strumienia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="112e3-180">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="112e3-181">Poprzednie przykładowe wywołanie to jeden przykład dwukierunkowego wywołania, które się zakończyło.</span><span class="sxs-lookup"><span data-stu-id="112e3-181">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="112e3-182">W wywołaniu klient:</span><span class="sxs-lookup"><span data-stu-id="112e3-182">In the call, the client:</span></span>

1. <span data-ttu-id="112e3-183">Uruchamia nowe dwukierunkowe wywołanie przesyłania strumieniowego przez wywołanie `EchoClient.Echo` .</span><span class="sxs-lookup"><span data-stu-id="112e3-183">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="112e3-184">Tworzy zadanie w tle do odczytywania wiadomości z usługi przy użyciu programu `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="112e3-184">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="112e3-185">Wysyła komunikaty do serwera przy użyciu programu `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="112e3-185">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="112e3-186">Powiadamia serwer, który zakończył wysyłanie komunikatów z programu `RequestStream.CompleteAsync()` .</span><span class="sxs-lookup"><span data-stu-id="112e3-186">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="112e3-187">Czeka, aż zadanie w tle odczytaje wszystkie wiadomości przychodzące.</span><span class="sxs-lookup"><span data-stu-id="112e3-187">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="112e3-188">W trakcie dwukierunkowego wywołania przesyłania strumieniowego klient i usługa mogą w dowolnym momencie wysyłać komunikaty do siebie.</span><span class="sxs-lookup"><span data-stu-id="112e3-188">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="112e3-189">Najlepsza logika klienta do współpracy z dwukierunkowym wywołaniem różni się w zależności od logiki usługi.</span><span class="sxs-lookup"><span data-stu-id="112e3-189">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="112e3-190">Dostęp do przyczep gRPC</span><span class="sxs-lookup"><span data-stu-id="112e3-190">Access gRPC trailers</span></span>

<span data-ttu-id="112e3-191">wywołania gRPC mogą zwracać gRPC przyczep.</span><span class="sxs-lookup"><span data-stu-id="112e3-191">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="112e3-192">przyczepy gRPC są używane do dostarczania metadanych nazw/wartości dotyczących wywołania.</span><span class="sxs-lookup"><span data-stu-id="112e3-192">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="112e3-193">Przyczepy zapewniają podobną funkcjonalność do nagłówków HTTP, ale są odbierane na końcu wywołania.</span><span class="sxs-lookup"><span data-stu-id="112e3-193">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="112e3-194">gRPC przyczep są dostępne przy użyciu `GetTrailers()` , która zwraca kolekcję metadanych.</span><span class="sxs-lookup"><span data-stu-id="112e3-194">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="112e3-195">Po zakończeniu odpowiedzi są zwracane przyczepy. w związku z tym przed uzyskaniem dostępu do przyczep należy oczekiwać wszystkich komunikatów odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="112e3-195">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="112e3-196">Wywołania jednoargumentowe i wywołanie przesyłania strumieniowego klienta muszą oczekiwać `ResponseAsync` przed wywołaniem `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="112e3-196">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="112e3-197">Wywołania przesyłania strumieniowego serwera i dwukierunkowe muszą kończyć się oczekiwaniem przed wywołaniem strumienia odpowiedzi `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="112e3-197">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

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

<span data-ttu-id="112e3-198">gRPC przyczepy są również dostępne z programu `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="112e3-198">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="112e3-199">Usługa może zwracać przyczepy ze stanem gRPC innym niż OK.</span><span class="sxs-lookup"><span data-stu-id="112e3-199">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="112e3-200">W tej sytuacji przyczepy są pobierane z wyjątku zgłoszonego przez klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="112e3-200">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="112e3-201">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="112e3-201">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
