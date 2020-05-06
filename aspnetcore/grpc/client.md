---
title: Wywoływanie usług gRPC przy użyciu klienta platformy .NET
author: jamesnk
description: Dowiedz się, jak wywoływać usługi gRPC Services za pomocą programu .NET gRPC Client.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: c554ce9702a9f2b2efeabbfdf0d1319ca4161a1c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774733"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="2aa4d-103">Wywoływanie usług gRPC przy użyciu klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="2aa4d-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="2aa4d-104">Biblioteka klienta .NET gRPC jest dostępna w pakiecie NuGet [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="2aa4d-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="2aa4d-105">W tym dokumencie wyjaśniono, jak:</span><span class="sxs-lookup"><span data-stu-id="2aa4d-105">This document explains how to:</span></span>

* <span data-ttu-id="2aa4d-106">Skonfiguruj klienta gRPC do wywoływania usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="2aa4d-107">GRPC wywołania jednoargumentowe, przesyłania strumieniowego serwera, przesyłania strumieniowego klienta i dwukierunkowych metod przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="2aa4d-108">Konfigurowanie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="2aa4d-108">Configure gRPC client</span></span>

<span data-ttu-id="2aa4d-109">gRPC klienci są konkretnymi typami klientów, które są [generowane z \* \*plików. proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="2aa4d-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="2aa4d-110">Konkretny klient gRPC ma metody, które są tłumaczone na usługę gRPC w pliku \* \*. proto\* .</span><span class="sxs-lookup"><span data-stu-id="2aa4d-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="2aa4d-111">Klient gRPC jest tworzony na podstawie kanału.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="2aa4d-112">Uruchom polecenie, `GrpcChannel.ForAddress` aby utworzyć kanał, a następnie użyj kanału, aby utworzyć klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="2aa4d-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="2aa4d-113">Kanał reprezentuje długotrwałe połączenie z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="2aa4d-114">Po utworzeniu kanału jest on konfigurowany z opcjami związanymi z wywoływaniem usługi.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="2aa4d-115">Na przykład `HttpClient` używane do wykonywania wywołań, maksymalnego rozmiaru wysyłania i odbierania wiadomości oraz rejestrowania można określić `GrpcChannelOptions` i użyć z. `GrpcChannel.ForAddress`</span><span class="sxs-lookup"><span data-stu-id="2aa4d-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="2aa4d-116">Aby uzyskać pełną listę opcji, zobacz [Opcje konfiguracji klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="2aa4d-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="2aa4d-117">Wydajność i użycie kanału i klienta:</span><span class="sxs-lookup"><span data-stu-id="2aa4d-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="2aa4d-118">Tworzenie kanału może być kosztowną operacją.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="2aa4d-119">Użycie kanału dla wywołań gRPC zapewnia korzyści wynikające z wydajności.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="2aa4d-120">gRPC klienci są tworzone za pomocą kanałów.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="2aa4d-121">gRPC klienci są obiektami lekkimi i nie muszą być buforowane ani ponownie używane.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="2aa4d-122">Wielu klientów gRPC można utworzyć na podstawie kanału, w tym różnych typów klientów.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="2aa4d-123">Kanał i klienci utworzeni z kanału mogą być bezpiecznie używani przez wiele wątków.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="2aa4d-124">Klienci utworzeni z kanału mogą wykonywać wiele jednoczesnych wywołań.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="2aa4d-125">`GrpcChannel.ForAddress`nie jest jedyną opcją tworzenia klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="2aa4d-126">W przypadku wywoływania usług gRPC z poziomu aplikacji ASP.NET Core należy wziąć pod uwagę [integrację klienta gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="2aa4d-126">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="2aa4d-127">Integracja gRPC z `HttpClientFactory` programem oferuje scentralizowaną alternatywę do tworzenia klientów gRPC.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="2aa4d-128">Dodatkowa konfiguracja jest wymagana do [wywołania niezabezpieczonych usług gRPC za pomocą klienta platformy .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="2aa4d-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="2aa4d-129">Wywoływanie gRPC za pośrednictwem protokołu `Grpc.Net.Client` http/2 z nie jest obecnie obsługiwane w oprogramowaniu Xamarin.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="2aa4d-130">Pracujemy nad ulepszeniem obsługi protokołu HTTP/2 w przyszłej wersji platformy Xamarin.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="2aa4d-131">[GRPC. Core](https://www.nuget.org/packages/Grpc.Core) i [GRPC-Web](xref:grpc/browser) to żywotne alternatywy, które działają dzisiaj.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="2aa4d-132">Utwórz wywołania gRPC</span><span class="sxs-lookup"><span data-stu-id="2aa4d-132">Make gRPC calls</span></span>

<span data-ttu-id="2aa4d-133">Wywołanie gRPC jest inicjowane przez wywołanie metody na kliencie.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="2aa4d-134">Klient gRPC będzie obsługiwał serializację komunikatów i odnoszący się do wywołania gRPC do prawidłowej usługi.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="2aa4d-135">gRPC ma różne typy metod.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-135">gRPC has different types of methods.</span></span> <span data-ttu-id="2aa4d-136">Sposób, w jaki klient jest używany do wykonywania wywołania gRPC, zależy od typu metody o nazwie.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-136">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="2aa4d-137">Typy metod gRPC są następujące:</span><span class="sxs-lookup"><span data-stu-id="2aa4d-137">The gRPC method types are:</span></span>

* <span data-ttu-id="2aa4d-138">Jednoargumentowy</span><span class="sxs-lookup"><span data-stu-id="2aa4d-138">Unary</span></span>
* <span data-ttu-id="2aa4d-139">Przesyłanie strumieniowe serwera</span><span class="sxs-lookup"><span data-stu-id="2aa4d-139">Server streaming</span></span>
* <span data-ttu-id="2aa4d-140">Przesyłanie strumieniowe klienta</span><span class="sxs-lookup"><span data-stu-id="2aa4d-140">Client streaming</span></span>
* <span data-ttu-id="2aa4d-141">Dwukierunkowe przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="2aa4d-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="2aa4d-142">Wywołanie jednoargumentowe</span><span class="sxs-lookup"><span data-stu-id="2aa4d-142">Unary call</span></span>

<span data-ttu-id="2aa4d-143">Wywołanie jednoargumentowe rozpoczyna się od klienta wysyłającego komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="2aa4d-144">Komunikat odpowiedzi jest zwracany po zakończeniu działania usługi.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="2aa4d-145">Każda Jednoargumentowa metoda usługi w pliku \* \*. proto\* powoduje dwie metody .NET na konkretnym typie klienta gRPC do wywoływania metody: Metoda asynchroniczna i Metoda blokująca.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="2aa4d-146">Na `GreeterClient` przykład istnieją dwa sposoby wywoływania `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="2aa4d-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="2aa4d-147">`GreeterClient.SayHelloAsync`-wywołuje `Greeter.SayHello` usługę asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="2aa4d-148">Można oczekiwać.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-148">Can be awaited.</span></span>
* <span data-ttu-id="2aa4d-149">`GreeterClient.SayHello`-wywołuje `Greeter.SayHello` usługę i bloki do momentu ukończenia.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="2aa4d-150">Nie używaj w kodzie asynchronicznym.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="2aa4d-151">Wywołanie przesyłania strumieniowego serwera</span><span class="sxs-lookup"><span data-stu-id="2aa4d-151">Server streaming call</span></span>

<span data-ttu-id="2aa4d-152">Wywołanie przesyłania strumieniowego serwera rozpoczyna się od klienta wysyłającego komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="2aa4d-153">`ResponseStream.MoveNext()`odczytuje komunikaty przesyłane strumieniowo z usługi.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="2aa4d-154">Wywołanie przesyłania strumieniowego serwera jest kompletne `ResponseStream.MoveNext()` , `false`gdy zwraca.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="2aa4d-155">W `await foreach` przypadku korzystania z języka C# 8 lub nowszego składnia może być używana do odczytywania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-155">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="2aa4d-156">Metoda `IAsyncStreamReader<T>.ReadAllAsync()` rozszerzająca odczytuje wszystkie komunikaty ze strumienia odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="2aa4d-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="2aa4d-157">Wywołanie przesyłania strumieniowego klienta</span><span class="sxs-lookup"><span data-stu-id="2aa4d-157">Client streaming call</span></span>

<span data-ttu-id="2aa4d-158">Wywołanie przesyłania strumieniowego klienta rozpoczyna się *bez* wysyłania komunikatu przez klienta.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="2aa4d-159">Klient może wysyłać wiadomości za pomocą `RequestStream.WriteAsync`polecenia.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="2aa4d-160">Gdy klient zakończył wysyłanie komunikatów, `RequestStream.CompleteAsync` powinien zostać wywołany w celu powiadomienia usługi.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-160">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="2aa4d-161">Wywołanie jest zakończone, gdy usługa zwróci komunikat odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-161">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="2aa4d-162">Dwukierunkowe wywołanie przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="2aa4d-162">Bi-directional streaming call</span></span>

<span data-ttu-id="2aa4d-163">Dwukierunkowe wywołanie przesyłania strumieniowego rozpoczyna się *bez* wysyłania komunikatu przez klienta.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="2aa4d-164">Klient może wysyłać wiadomości za pomocą `RequestStream.WriteAsync`polecenia.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="2aa4d-165">Komunikaty przesyłane strumieniowo z usługi są dostępne z `ResponseStream.MoveNext()` lub `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="2aa4d-166">Dwukierunkowe wywołanie przesyłania strumieniowego jest kompletne, gdy `ResponseStream` nie ma więcej komunikatów.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="2aa4d-167">W trakcie dwukierunkowego wywołania przesyłania strumieniowego klient i usługa mogą w dowolnym momencie wysyłać komunikaty do siebie.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="2aa4d-168">Najlepsza logika klienta do współpracy z dwukierunkowym wywołaniem różni się w zależności od logiki usługi.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="2aa4d-169">Dostęp do przyczep gRPC</span><span class="sxs-lookup"><span data-stu-id="2aa4d-169">Access gRPC trailers</span></span>

<span data-ttu-id="2aa4d-170">wywołania gRPC mogą zwracać gRPC przyczep.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-170">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="2aa4d-171">przyczepy gRPC są używane do dostarczania metadanych nazw/wartości dotyczących wywołania.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-171">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="2aa4d-172">Przyczepy zapewniają podobną funkcjonalność do nagłówków HTTP, ale są odbierane na końcu wywołania.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-172">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="2aa4d-173">gRPC przyczep są dostępne przy `GetTrailers()`użyciu, która zwraca kolekcję metadanych.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-173">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="2aa4d-174">Po zakończeniu odpowiedzi są zwracane przyczepy. w związku z tym przed uzyskaniem dostępu do przyczep należy oczekiwać wszystkich komunikatów odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-174">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="2aa4d-175">Wywołania jednoargumentowe i wywołanie przesyłania `ResponseAsync` strumieniowego `GetTrailers()`klienta muszą oczekiwać przed wywołaniem:</span><span class="sxs-lookup"><span data-stu-id="2aa4d-175">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="2aa4d-176">Wywołania przesyłania strumieniowego serwera i dwukierunkowe muszą kończyć się oczekiwaniem przed wywołaniem `GetTrailers()`strumienia odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="2aa4d-176">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="2aa4d-177">gRPC przyczepy są również dostępne z `RpcException`programu.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-177">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="2aa4d-178">Usługa może zwracać przyczepy ze stanem gRPC innym niż OK.</span><span class="sxs-lookup"><span data-stu-id="2aa4d-178">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="2aa4d-179">W tej sytuacji przyczepy są pobierane z wyjątku zgłoszonego przez klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="2aa4d-179">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="2aa4d-180">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2aa4d-180">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
