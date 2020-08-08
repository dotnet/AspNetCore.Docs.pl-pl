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
ms.openlocfilehash: 5aca81da34e5ed51b2dc4f404c1ba4d7377a422f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016248"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="6ace5-103">Wywoływanie usług gRPC przy użyciu klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="6ace5-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="6ace5-104">Biblioteka klienta .NET gRPC jest dostępna w pakiecie NuGet [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="6ace5-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="6ace5-105">W tym dokumencie wyjaśniono, jak:</span><span class="sxs-lookup"><span data-stu-id="6ace5-105">This document explains how to:</span></span>

* <span data-ttu-id="6ace5-106">Skonfiguruj klienta gRPC do wywoływania usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ace5-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="6ace5-107">GRPC wywołania jednoargumentowe, przesyłania strumieniowego serwera, przesyłania strumieniowego klienta i dwukierunkowych metod przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="6ace5-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="6ace5-108">Konfigurowanie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="6ace5-108">Configure gRPC client</span></span>

<span data-ttu-id="6ace5-109">gRPC klienci są konkretnymi typami klientów, które są [generowane z plików \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="6ace5-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="6ace5-110">Konkretny klient gRPC ma metody, które są tłumaczone na usługę gRPC w pliku \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="6ace5-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="6ace5-111">Klient gRPC jest tworzony na podstawie kanału.</span><span class="sxs-lookup"><span data-stu-id="6ace5-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="6ace5-112">Uruchom `GrpcChannel.ForAddress` polecenie, aby utworzyć kanał, a następnie użyj kanału, aby utworzyć klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="6ace5-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="6ace5-113">Kanał reprezentuje długotrwałe połączenie z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ace5-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="6ace5-114">Po utworzeniu kanału jest on konfigurowany z opcjami związanymi z wywoływaniem usługi.</span><span class="sxs-lookup"><span data-stu-id="6ace5-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="6ace5-115">Na przykład `HttpClient` używane do wykonywania wywołań, maksymalnego rozmiaru wysyłania i odbierania wiadomości oraz rejestrowania można określić `GrpcChannelOptions` i użyć z `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="6ace5-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="6ace5-116">Aby uzyskać pełną listę opcji, zobacz [Opcje konfiguracji klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="6ace5-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="6ace5-117">Konfigurowanie protokołu TLS</span><span class="sxs-lookup"><span data-stu-id="6ace5-117">Configure TLS</span></span>

<span data-ttu-id="6ace5-118">Klient gRPC musi używać tego samego zabezpieczenia na poziomie połączenia co nazwana usługa.</span><span class="sxs-lookup"><span data-stu-id="6ace5-118">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="6ace5-119">gRPC Client Transport Layer Security (TLS) jest konfigurowany podczas tworzenia kanału gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ace5-119">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="6ace5-120">Klient gRPC zgłasza błąd podczas wywoływania usługi, a zabezpieczenia na poziomie połączenia kanału i usługi nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="6ace5-120">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="6ace5-121">Aby skonfigurować kanał gRPC do korzystania z protokołu TLS, upewnij się, że adres serwera zaczyna się od `https` .</span><span class="sxs-lookup"><span data-stu-id="6ace5-121">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="6ace5-122">Na przykład `GrpcChannel.ForAddress("https://localhost:5001")` używa protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6ace5-122">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="6ace5-123">Kanał gRPC automatycznie negotates połączenie zabezpieczone przez protokół TLS i używa bezpiecznego połączenia do wykonywania wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ace5-123">The gRPC channel automatically negotates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="6ace5-124">gRPC obsługuje uwierzytelnianie certyfikatu klienta za pośrednictwem protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="6ace5-124">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="6ace5-125">Informacje dotyczące konfigurowania certyfikatów klienta przy użyciu kanału gRPC można znaleźć w temacie <xref:grpc/authn-and-authz#client-certificate-authentication> .</span><span class="sxs-lookup"><span data-stu-id="6ace5-125">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="6ace5-126">Aby wywoływać niezabezpieczone usługi gRPC, upewnij się, że adres serwera zaczyna się od `http` .</span><span class="sxs-lookup"><span data-stu-id="6ace5-126">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="6ace5-127">Na przykład `GrpcChannel.ForAddress("http://localhost:5000")` używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ace5-127">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="6ace5-128">W przypadku platformy .NET Core 3,1 lub nowszej dodatkowa konfiguracja jest wymagana do [wywołania niezabezpieczonych usług gRPC z klientem platformy .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="6ace5-128">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="6ace5-129">Wydajność klienta</span><span class="sxs-lookup"><span data-stu-id="6ace5-129">Client performance</span></span>

<span data-ttu-id="6ace5-130">Wydajność i użycie kanału i klienta:</span><span class="sxs-lookup"><span data-stu-id="6ace5-130">Channel and client performance and usage:</span></span>

* <span data-ttu-id="6ace5-131">Tworzenie kanału może być kosztowną operacją.</span><span class="sxs-lookup"><span data-stu-id="6ace5-131">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="6ace5-132">Użycie kanału dla wywołań gRPC zapewnia korzyści wynikające z wydajności.</span><span class="sxs-lookup"><span data-stu-id="6ace5-132">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="6ace5-133">gRPC klienci są tworzone za pomocą kanałów.</span><span class="sxs-lookup"><span data-stu-id="6ace5-133">gRPC clients are created with channels.</span></span> <span data-ttu-id="6ace5-134">gRPC klienci są obiektami lekkimi i nie muszą być buforowane ani ponownie używane.</span><span class="sxs-lookup"><span data-stu-id="6ace5-134">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="6ace5-135">Wielu klientów gRPC można utworzyć na podstawie kanału, w tym różnych typów klientów.</span><span class="sxs-lookup"><span data-stu-id="6ace5-135">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="6ace5-136">Kanał i klienci utworzeni z kanału mogą być bezpiecznie używani przez wiele wątków.</span><span class="sxs-lookup"><span data-stu-id="6ace5-136">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="6ace5-137">Klienci utworzeni z kanału mogą wykonywać wiele jednoczesnych wywołań.</span><span class="sxs-lookup"><span data-stu-id="6ace5-137">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="6ace5-138">`GrpcChannel.ForAddress`nie jest jedyną opcją tworzenia klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ace5-138">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="6ace5-139">W przypadku wywoływania usług gRPC z poziomu aplikacji ASP.NET Core należy wziąć pod uwagę [integrację klienta gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="6ace5-139">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="6ace5-140">Integracja gRPC z programem `HttpClientFactory` oferuje scentralizowaną alternatywę do tworzenia klientów gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ace5-140">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="6ace5-141">Wywoływanie gRPC za pośrednictwem protokołu HTTP/2 z `Grpc.Net.Client` nie jest obecnie obsługiwane w oprogramowaniu Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6ace5-141">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="6ace5-142">Pracujemy nad ulepszeniem obsługi protokołu HTTP/2 w przyszłej wersji platformy Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6ace5-142">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="6ace5-143">[GRPC. Core](https://www.nuget.org/packages/Grpc.Core) i [GRPC-Web](xref:grpc/browser) to żywotne alternatywy, które działają dzisiaj.</span><span class="sxs-lookup"><span data-stu-id="6ace5-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="6ace5-144">Utwórz wywołania gRPC</span><span class="sxs-lookup"><span data-stu-id="6ace5-144">Make gRPC calls</span></span>

<span data-ttu-id="6ace5-145">Wywołanie gRPC jest inicjowane przez wywołanie metody na kliencie.</span><span class="sxs-lookup"><span data-stu-id="6ace5-145">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="6ace5-146">Klient gRPC będzie obsługiwał serializację komunikatów i odnoszący się do wywołania gRPC do prawidłowej usługi.</span><span class="sxs-lookup"><span data-stu-id="6ace5-146">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="6ace5-147">gRPC ma różne typy metod.</span><span class="sxs-lookup"><span data-stu-id="6ace5-147">gRPC has different types of methods.</span></span> <span data-ttu-id="6ace5-148">Sposób, w jaki klient jest używany do wykonywania wywołania gRPC, zależy od typu metody o nazwie.</span><span class="sxs-lookup"><span data-stu-id="6ace5-148">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="6ace5-149">Typy metod gRPC są następujące:</span><span class="sxs-lookup"><span data-stu-id="6ace5-149">The gRPC method types are:</span></span>

* <span data-ttu-id="6ace5-150">Jednoargumentowy</span><span class="sxs-lookup"><span data-stu-id="6ace5-150">Unary</span></span>
* <span data-ttu-id="6ace5-151">Przesyłanie strumieniowe serwera</span><span class="sxs-lookup"><span data-stu-id="6ace5-151">Server streaming</span></span>
* <span data-ttu-id="6ace5-152">Przesyłanie strumieniowe klienta</span><span class="sxs-lookup"><span data-stu-id="6ace5-152">Client streaming</span></span>
* <span data-ttu-id="6ace5-153">Dwukierunkowe przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="6ace5-153">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="6ace5-154">Wywołanie jednoargumentowe</span><span class="sxs-lookup"><span data-stu-id="6ace5-154">Unary call</span></span>

<span data-ttu-id="6ace5-155">Wywołanie jednoargumentowe rozpoczyna się od klienta wysyłającego komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="6ace5-155">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="6ace5-156">Komunikat odpowiedzi jest zwracany po zakończeniu działania usługi.</span><span class="sxs-lookup"><span data-stu-id="6ace5-156">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="6ace5-157">Każda Jednoargumentowa metoda usługi w pliku \* \* . proto\* powoduje dwie metody .NET na konkretnym typie klienta gRPC do wywoływania metody: Metoda asynchroniczna i Metoda blokująca.</span><span class="sxs-lookup"><span data-stu-id="6ace5-157">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="6ace5-158">Na przykład istnieją `GreeterClient` dwa sposoby wywoływania `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="6ace5-158">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="6ace5-159">`GreeterClient.SayHelloAsync`-wywołuje `Greeter.SayHello` usługę asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="6ace5-159">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="6ace5-160">Można oczekiwać.</span><span class="sxs-lookup"><span data-stu-id="6ace5-160">Can be awaited.</span></span>
* <span data-ttu-id="6ace5-161">`GreeterClient.SayHello`-wywołuje `Greeter.SayHello` usługę i bloki do momentu ukończenia.</span><span class="sxs-lookup"><span data-stu-id="6ace5-161">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="6ace5-162">Nie używaj w kodzie asynchronicznym.</span><span class="sxs-lookup"><span data-stu-id="6ace5-162">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="6ace5-163">Wywołanie przesyłania strumieniowego serwera</span><span class="sxs-lookup"><span data-stu-id="6ace5-163">Server streaming call</span></span>

<span data-ttu-id="6ace5-164">Wywołanie przesyłania strumieniowego serwera rozpoczyna się od klienta wysyłającego komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="6ace5-164">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="6ace5-165">`ResponseStream.MoveNext()`odczytuje komunikaty przesyłane strumieniowo z usługi.</span><span class="sxs-lookup"><span data-stu-id="6ace5-165">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="6ace5-166">Wywołanie przesyłania strumieniowego serwera jest kompletne, gdy `ResponseStream.MoveNext()` zwraca `false` .</span><span class="sxs-lookup"><span data-stu-id="6ace5-166">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="6ace5-167">W przypadku korzystania z języka C# 8 lub nowszego `await foreach` składnia może być używana do odczytywania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="6ace5-167">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="6ace5-168">`IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozszerzająca odczytuje wszystkie komunikaty ze strumienia odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="6ace5-168">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="6ace5-169">Wywołanie przesyłania strumieniowego klienta</span><span class="sxs-lookup"><span data-stu-id="6ace5-169">Client streaming call</span></span>

<span data-ttu-id="6ace5-170">Wywołanie przesyłania strumieniowego klienta rozpoczyna się *bez* wysyłania komunikatu przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6ace5-170">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="6ace5-171">Klient może wysyłać wiadomości za pomocą polecenia `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="6ace5-171">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="6ace5-172">Gdy klient zakończył wysyłanie komunikatów, `RequestStream.CompleteAsync` powinien zostać wywołany w celu powiadomienia usługi.</span><span class="sxs-lookup"><span data-stu-id="6ace5-172">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="6ace5-173">Wywołanie jest zakończone, gdy usługa zwróci komunikat odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6ace5-173">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="6ace5-174">Dwukierunkowe wywołanie przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="6ace5-174">Bi-directional streaming call</span></span>

<span data-ttu-id="6ace5-175">Dwukierunkowe wywołanie przesyłania strumieniowego rozpoczyna się *bez* wysyłania komunikatu przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6ace5-175">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="6ace5-176">Klient może wysyłać wiadomości za pomocą polecenia `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="6ace5-176">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="6ace5-177">Komunikaty przesyłane strumieniowo z usługi są dostępne z `ResponseStream.MoveNext()` lub `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="6ace5-177">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="6ace5-178">Dwukierunkowe wywołanie przesyłania strumieniowego jest kompletne, gdy `ResponseStream` nie ma więcej komunikatów.</span><span class="sxs-lookup"><span data-stu-id="6ace5-178">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="6ace5-179">W trakcie dwukierunkowego wywołania przesyłania strumieniowego klient i usługa mogą w dowolnym momencie wysyłać komunikaty do siebie.</span><span class="sxs-lookup"><span data-stu-id="6ace5-179">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="6ace5-180">Najlepsza logika klienta do współpracy z dwukierunkowym wywołaniem różni się w zależności od logiki usługi.</span><span class="sxs-lookup"><span data-stu-id="6ace5-180">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="6ace5-181">Dostęp do przyczep gRPC</span><span class="sxs-lookup"><span data-stu-id="6ace5-181">Access gRPC trailers</span></span>

<span data-ttu-id="6ace5-182">wywołania gRPC mogą zwracać gRPC przyczep.</span><span class="sxs-lookup"><span data-stu-id="6ace5-182">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="6ace5-183">przyczepy gRPC są używane do dostarczania metadanych nazw/wartości dotyczących wywołania.</span><span class="sxs-lookup"><span data-stu-id="6ace5-183">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="6ace5-184">Przyczepy zapewniają podobną funkcjonalność do nagłówków HTTP, ale są odbierane na końcu wywołania.</span><span class="sxs-lookup"><span data-stu-id="6ace5-184">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="6ace5-185">gRPC przyczep są dostępne przy użyciu `GetTrailers()` , która zwraca kolekcję metadanych.</span><span class="sxs-lookup"><span data-stu-id="6ace5-185">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="6ace5-186">Po zakończeniu odpowiedzi są zwracane przyczepy. w związku z tym przed uzyskaniem dostępu do przyczep należy oczekiwać wszystkich komunikatów odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6ace5-186">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="6ace5-187">Wywołania jednoargumentowe i wywołanie przesyłania strumieniowego klienta muszą oczekiwać `ResponseAsync` przed wywołaniem `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="6ace5-187">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="6ace5-188">Wywołania przesyłania strumieniowego serwera i dwukierunkowe muszą kończyć się oczekiwaniem przed wywołaniem strumienia odpowiedzi `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="6ace5-188">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

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

<span data-ttu-id="6ace5-189">gRPC przyczepy są również dostępne z programu `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="6ace5-189">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="6ace5-190">Usługa może zwracać przyczepy ze stanem gRPC innym niż OK.</span><span class="sxs-lookup"><span data-stu-id="6ace5-190">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="6ace5-191">W tej sytuacji przyczepy są pobierane z wyjątku zgłoszonego przez klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="6ace5-191">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="6ace5-192">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="6ace5-192">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
