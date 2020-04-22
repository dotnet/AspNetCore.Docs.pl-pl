---
title: Wywoływanie usług gRPC przy użyciu klienta platformy .NET
author: jamesnk
description: Dowiedz się, jak dzwonić do usług gRPC z klientem gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
uid: grpc/client
ms.openlocfilehash: aefa52a5c4c66178c5978aebd4cd9b00559c7f54
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791546"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="ac204-103">Wywoływanie usług gRPC przy użyciu klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="ac204-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="ac204-104">Biblioteka klienta gRPC .NET jest dostępna w pakiecie [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet.</span><span class="sxs-lookup"><span data-stu-id="ac204-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="ac204-105">W tym dokumencie wyjaśniono, jak:</span><span class="sxs-lookup"><span data-stu-id="ac204-105">This document explains how to:</span></span>

* <span data-ttu-id="ac204-106">Skonfiguruj klienta gRPC, aby dzwonił do usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="ac204-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="ac204-107">Wykonuj wywołania gRPC do arysowych, strumieniowych serwerów, przesyłania strumieniowego klienta i dwukierunkowych metod przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="ac204-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="ac204-108">Konfigurowanie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="ac204-108">Configure gRPC client</span></span>

<span data-ttu-id="ac204-109">Klienci gRPC to konkretne typy klientów [generowane z \* \*plików .proto.\* ](xref:grpc/basics#generated-c-assets)</span><span class="sxs-lookup"><span data-stu-id="ac204-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="ac204-110">Konkretny klient gRPC ma metody, które przekładają się na usługę gRPC w pliku \* \*.proto.\*</span><span class="sxs-lookup"><span data-stu-id="ac204-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="ac204-111">Klient gRPC jest tworzony z kanału.</span><span class="sxs-lookup"><span data-stu-id="ac204-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="ac204-112">Zacznij od `GrpcChannel.ForAddress` utworzenia kanału, a następnie użyj kanału, aby utworzyć klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="ac204-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="ac204-113">Kanał reprezentuje długotrwałe połączenie z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="ac204-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="ac204-114">Po utworzeniu kanału jest on konfigurowany z opcjami związanymi z wywoływaniem usługi.</span><span class="sxs-lookup"><span data-stu-id="ac204-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="ac204-115">Na przykład `HttpClient` używany do nawiązywania połączeń, maksymalny rozmiar wiadomości wysyłania i odbierania oraz rejestrowanie można określić `GrpcChannelOptions` i używać z programem `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="ac204-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="ac204-116">Aby uzyskać pełną listę opcji, zobacz [opcje konfiguracji klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="ac204-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="ac204-117">Wydajność i wykorzystanie kanałów i klientów:</span><span class="sxs-lookup"><span data-stu-id="ac204-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="ac204-118">Tworzenie kanału może być kosztowną operacją.</span><span class="sxs-lookup"><span data-stu-id="ac204-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="ac204-119">Ponowne korzystać z kanału dla połączeń gRPC zapewnia korzyści wydajności.</span><span class="sxs-lookup"><span data-stu-id="ac204-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="ac204-120">Klienci gRPC są tworzone za pomocą kanałów.</span><span class="sxs-lookup"><span data-stu-id="ac204-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="ac204-121">Klienci gRPC są lekkimi obiektami i nie muszą być buforowane ani ponownie zażywane.</span><span class="sxs-lookup"><span data-stu-id="ac204-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="ac204-122">Wielu klientów gRPC można utworzyć z kanału, w tym różnych typów klientów.</span><span class="sxs-lookup"><span data-stu-id="ac204-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="ac204-123">Kanał i klientów utworzonych z kanału mogą być bezpiecznie używane przez wiele wątków.</span><span class="sxs-lookup"><span data-stu-id="ac204-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="ac204-124">Klienci utworzone z kanału można wykonywać wiele jednoczesnych połączeń.</span><span class="sxs-lookup"><span data-stu-id="ac204-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="ac204-125">`GrpcChannel.ForAddress`nie jest jedyną opcją tworzenia klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="ac204-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="ac204-126">W przypadku wywoływania usług gRPC z aplikacji ASP.NET Core należy rozważyć [integrację fabryki klienta gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="ac204-126">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="ac204-127">Integracja gRPC z `HttpClientFactory` oferuje scentralizowaną alternatywę dla tworzenia klientów gRPC.</span><span class="sxs-lookup"><span data-stu-id="ac204-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="ac204-128">Dodatkowa konfiguracja jest wymagana do [wywoływania niezabezpieczonych usług gRPC z klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="ac204-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="ac204-129">Wywołanie gRPC przez HTTP/2 z `Grpc.Net.Client` obecnie nie jest obsługiwane na xamarin.</span><span class="sxs-lookup"><span data-stu-id="ac204-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="ac204-130">Pracujemy nad ulepszeniem obsługi protokołu HTTP/2 w przyszłej wersji platformy Xamarin.</span><span class="sxs-lookup"><span data-stu-id="ac204-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="ac204-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) i [gRPC-Web](xref:grpc/browser) są realnymi alternatywami, które działają dzisiaj.</span><span class="sxs-lookup"><span data-stu-id="ac204-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="ac204-132">Wykonywanie połączeń gRPC</span><span class="sxs-lookup"><span data-stu-id="ac204-132">Make gRPC calls</span></span>

<span data-ttu-id="ac204-133">Wywołanie gRPC jest inicjowane przez wywołanie metody na kliencie.</span><span class="sxs-lookup"><span data-stu-id="ac204-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="ac204-134">Klient gRPC będzie obsługiwać serializacji wiadomości i adresowania wywołania gRPC do poprawnej usługi.</span><span class="sxs-lookup"><span data-stu-id="ac204-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="ac204-135">gRPC ma różne rodzaje metod.</span><span class="sxs-lookup"><span data-stu-id="ac204-135">gRPC has different types of methods.</span></span> <span data-ttu-id="ac204-136">Sposób, w jaki klient jest używany do wywołania gRPC zależy od typu metody wywoływanej.</span><span class="sxs-lookup"><span data-stu-id="ac204-136">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="ac204-137">Typy metod gRPC to:</span><span class="sxs-lookup"><span data-stu-id="ac204-137">The gRPC method types are:</span></span>

* <span data-ttu-id="ac204-138">Jednoargumentowy</span><span class="sxs-lookup"><span data-stu-id="ac204-138">Unary</span></span>
* <span data-ttu-id="ac204-139">Przesyłanie strumieniowe serwera</span><span class="sxs-lookup"><span data-stu-id="ac204-139">Server streaming</span></span>
* <span data-ttu-id="ac204-140">Przesyłanie strumieniowe klienta</span><span class="sxs-lookup"><span data-stu-id="ac204-140">Client streaming</span></span>
* <span data-ttu-id="ac204-141">Dwukierunkowe przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="ac204-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="ac204-142">Połączenie bezemisowe</span><span class="sxs-lookup"><span data-stu-id="ac204-142">Unary call</span></span>

<span data-ttu-id="ac204-143">Połączenie bezemisowe rozpoczyna się od wysłania przez klienta wiadomości żądania.</span><span class="sxs-lookup"><span data-stu-id="ac204-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="ac204-144">Komunikat odpowiedzi jest zwracany po zakończeniu usługi.</span><span class="sxs-lookup"><span data-stu-id="ac204-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="ac204-145">Każda metoda usługi dwuarchifowej w pliku \* \*.proto\* spowoduje dwie metody .NET na typie klienta gRPC dla wywoływania metody: metoda asynchroniza i metoda blokowania.</span><span class="sxs-lookup"><span data-stu-id="ac204-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="ac204-146">Na przykład, `GreeterClient` na istnieją dwa `SayHello`sposoby wywoływania:</span><span class="sxs-lookup"><span data-stu-id="ac204-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="ac204-147">`GreeterClient.SayHelloAsync`- `Greeter.SayHello` wywołuje usługę asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="ac204-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="ac204-148">Można oczekiwać.</span><span class="sxs-lookup"><span data-stu-id="ac204-148">Can be awaited.</span></span>
* <span data-ttu-id="ac204-149">`GreeterClient.SayHello`- `Greeter.SayHello` dzwoni serwis i blokuje się do czasu zakończenia.</span><span class="sxs-lookup"><span data-stu-id="ac204-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="ac204-150">Nie używaj w kodzie asynchroniza.</span><span class="sxs-lookup"><span data-stu-id="ac204-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="ac204-151">Wywołanie przesyłania strumieniowego serwera</span><span class="sxs-lookup"><span data-stu-id="ac204-151">Server streaming call</span></span>

<span data-ttu-id="ac204-152">Wywołanie przesyłania strumieniowego serwera rozpoczyna się od wysłania przez klienta komunikatu żądania.</span><span class="sxs-lookup"><span data-stu-id="ac204-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="ac204-153">`ResponseStream.MoveNext()`odczytuje wiadomości przesyłane strumieniowo z usługi.</span><span class="sxs-lookup"><span data-stu-id="ac204-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="ac204-154">Połączenie przesyłania strumieniowego `ResponseStream.MoveNext()` serwera `false`zostanie zakończone po zwróceniu .</span><span class="sxs-lookup"><span data-stu-id="ac204-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="ac204-155">Podczas korzystania z języka C# `await foreach` 8 lub nowszego składni może służyć do odczytywania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="ac204-155">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="ac204-156">Metoda `IAsyncStreamReader<T>.ReadAllAsync()` rozszerzenia odczytuje wszystkie komunikaty ze strumienia odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="ac204-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="ac204-157">Połączenie strumieniowe klienta</span><span class="sxs-lookup"><span data-stu-id="ac204-157">Client streaming call</span></span>

<span data-ttu-id="ac204-158">Połączenie przesyłania strumieniowego klienta rozpoczyna się *bez* wysyłania wiadomości przez klienta.</span><span class="sxs-lookup"><span data-stu-id="ac204-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="ac204-159">Klient może wybrać opcję `RequestStream.WriteAsync`wysyłania wiadomości za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="ac204-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="ac204-160">Po zakończeniu wysyłania wiadomości `RequestStream.CompleteAsync` przez klienta należy wywołać powiadomienie usługi.</span><span class="sxs-lookup"><span data-stu-id="ac204-160">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="ac204-161">Wywołanie jest zakończone, gdy usługa zwraca komunikat odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ac204-161">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="ac204-162">Dwukierunkowe połączenie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="ac204-162">Bi-directional streaming call</span></span>

<span data-ttu-id="ac204-163">Dwukierunkowe połączenie przesyłania strumieniowego rozpoczyna się *bez* wysyłania wiadomości przez klienta.</span><span class="sxs-lookup"><span data-stu-id="ac204-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="ac204-164">Klient może wybrać opcję `RequestStream.WriteAsync`wysyłania wiadomości za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="ac204-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="ac204-165">Wiadomości przesyłane strumieniowo z `ResponseStream.MoveNext()` `ResponseStream.ReadAllAsync()`usługi są dostępne za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="ac204-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="ac204-166">Dwukierunkowe wywołanie przesyłania strumieniowego `ResponseStream` jest zakończone, gdy nie ma więcej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="ac204-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="ac204-167">Podczas dwukierunkowego połączenia przesyłania strumieniowego klient i usługa mogą wysyłać do siebie wiadomości w dowolnym momencie.</span><span class="sxs-lookup"><span data-stu-id="ac204-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="ac204-168">Najlepsza logika klienta do interakcji z wywołaniem dwukierunkowym różni się w zależności od logiki usługi.</span><span class="sxs-lookup"><span data-stu-id="ac204-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="ac204-169">Dostęp do przyczep gRPC</span><span class="sxs-lookup"><span data-stu-id="ac204-169">Access gRPC trailers</span></span>

<span data-ttu-id="ac204-170">połączenia gRPC mogą zwracać przyczepy grpc.</span><span class="sxs-lookup"><span data-stu-id="ac204-170">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="ac204-171">Przyczepy gRPC służą do podawania metadanych nazwy/wartości o połączeniu.</span><span class="sxs-lookup"><span data-stu-id="ac204-171">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="ac204-172">Zwiastuny zapewniają podobną funkcjonalność do nagłówków HTTP, ale są odbierane po zakończeniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="ac204-172">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="ac204-173">Przyczepy gRPC są `GetTrailers()`dostępne za pomocą , który zwraca zbiór metadanych.</span><span class="sxs-lookup"><span data-stu-id="ac204-173">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="ac204-174">Przyczepy są zwracane po zakończeniu odpowiedzi, dlatego przed wejściem do przyczep należy poczekać na wszystkie komunikaty odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ac204-174">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="ac204-175">Połączenia przesyłane strumieniowo `ResponseAsync` bez `GetTrailers()`emigo i klientów muszą czekać przed wywołaniem:</span><span class="sxs-lookup"><span data-stu-id="ac204-175">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="ac204-176">Serwer i dwukierunkowe połączenia przesyłania strumieniowego muszą `GetTrailers()`zakończyć się oczekiwaniem na strumień odpowiedzi przed wywołaniem:</span><span class="sxs-lookup"><span data-stu-id="ac204-176">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

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

<span data-ttu-id="ac204-177">Przyczepy gRPC są `RpcException`również dostępne z .</span><span class="sxs-lookup"><span data-stu-id="ac204-177">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="ac204-178">Usługa może zwrócić przyczepy wraz ze statusem gRPC nienajmki.</span><span class="sxs-lookup"><span data-stu-id="ac204-178">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="ac204-179">W tej sytuacji przyczepy są pobierane z wyjątku zgłaszanych przez klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="ac204-179">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ac204-180">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="ac204-180">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
