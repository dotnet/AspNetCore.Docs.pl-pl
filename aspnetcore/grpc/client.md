---
title: Dzwoń z usług gRPC za pomocą klienta .NET
author: jamesnk
description: Dowiedz się, jak dzwonić do usług gRPC z klientem gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667175"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="0567a-103">Dzwoń z usług gRPC za pomocą klienta .NET</span><span class="sxs-lookup"><span data-stu-id="0567a-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="0567a-104">Biblioteka klienta gRPC .NET jest dostępna w pakiecie [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet.</span><span class="sxs-lookup"><span data-stu-id="0567a-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="0567a-105">W tym dokumencie wyjaśniono, jak:</span><span class="sxs-lookup"><span data-stu-id="0567a-105">This document explains how to:</span></span>

* <span data-ttu-id="0567a-106">Skonfiguruj klienta gRPC, aby dzwonił do usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="0567a-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="0567a-107">Wykonuj wywołania gRPC do arysowych, strumieniowych serwerów, przesyłania strumieniowego klienta i dwukierunkowych metod przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="0567a-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="0567a-108">Konfigurowanie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="0567a-108">Configure gRPC client</span></span>

<span data-ttu-id="0567a-109">Klienci gRPC to konkretne typy klientów [generowane z \* \*plików .proto.\* ](xref:grpc/basics#generated-c-assets)</span><span class="sxs-lookup"><span data-stu-id="0567a-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="0567a-110">Konkretny klient gRPC ma metody, które przekładają się na usługę gRPC w pliku \* \*.proto.\*</span><span class="sxs-lookup"><span data-stu-id="0567a-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="0567a-111">Klient gRPC jest tworzony z kanału.</span><span class="sxs-lookup"><span data-stu-id="0567a-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="0567a-112">Zacznij od `GrpcChannel.ForAddress` utworzenia kanału, a następnie użyj kanału, aby utworzyć klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="0567a-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="0567a-113">Kanał reprezentuje długotrwałe połączenie z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="0567a-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="0567a-114">Po utworzeniu kanału jest on konfigurowany z opcjami związanymi z wywoływaniem usługi.</span><span class="sxs-lookup"><span data-stu-id="0567a-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="0567a-115">Na przykład `HttpClient` używany do nawiązywania połączeń, maksymalny rozmiar wiadomości wysyłania i odbierania oraz rejestrowanie można określić `GrpcChannelOptions` i używać z programem `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="0567a-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="0567a-116">Aby uzyskać pełną listę opcji, zobacz [opcje konfiguracji klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="0567a-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="0567a-117">Wydajność i wykorzystanie kanałów i klientów:</span><span class="sxs-lookup"><span data-stu-id="0567a-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="0567a-118">Tworzenie kanału może być kosztowną operacją.</span><span class="sxs-lookup"><span data-stu-id="0567a-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="0567a-119">Ponowne korzystać z kanału dla połączeń gRPC zapewnia korzyści wydajności.</span><span class="sxs-lookup"><span data-stu-id="0567a-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="0567a-120">Klienci gRPC są tworzone za pomocą kanałów.</span><span class="sxs-lookup"><span data-stu-id="0567a-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="0567a-121">Klienci gRPC są lekkimi obiektami i nie muszą być buforowane ani ponownie zażywane.</span><span class="sxs-lookup"><span data-stu-id="0567a-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="0567a-122">Wielu klientów gRPC można utworzyć z kanału, w tym różnych typów klientów.</span><span class="sxs-lookup"><span data-stu-id="0567a-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="0567a-123">Kanał i klientów utworzonych z kanału mogą być bezpiecznie używane przez wiele wątków.</span><span class="sxs-lookup"><span data-stu-id="0567a-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="0567a-124">Klienci utworzone z kanału można wykonywać wiele jednoczesnych połączeń.</span><span class="sxs-lookup"><span data-stu-id="0567a-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="0567a-125">`GrpcChannel.ForAddress`nie jest jedyną opcją tworzenia klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="0567a-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="0567a-126">Jeśli dzwonisz do usług gRPC z aplikacji ASP.NET Core, rozważ [integrację fabryki klienta gRPC.](xref:grpc/clientfactory)</span><span class="sxs-lookup"><span data-stu-id="0567a-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="0567a-127">Integracja gRPC z `HttpClientFactory` oferuje scentralizowaną alternatywę dla tworzenia klientów gRPC.</span><span class="sxs-lookup"><span data-stu-id="0567a-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="0567a-128">Dodatkowa konfiguracja jest wymagana do [wywoływania niezabezpieczonych usług gRPC z klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="0567a-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="0567a-129">Wywołanie gRPC przez HTTP/2 z `Grpc.Net.Client` obecnie nie jest obsługiwane na xamarin.</span><span class="sxs-lookup"><span data-stu-id="0567a-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="0567a-130">Pracujemy nad ulepszeniem obsługi protokołu HTTP/2 w przyszłej wersji platformy Xamarin.</span><span class="sxs-lookup"><span data-stu-id="0567a-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="0567a-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) i [gRPC-Web](xref:grpc/browser) są realnymi alternatywami, które działają dzisiaj.</span><span class="sxs-lookup"><span data-stu-id="0567a-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="0567a-132">Wykonywanie połączeń gRPC</span><span class="sxs-lookup"><span data-stu-id="0567a-132">Make gRPC calls</span></span>

<span data-ttu-id="0567a-133">Wywołanie gRPC jest inicjowane przez wywołanie metody na kliencie.</span><span class="sxs-lookup"><span data-stu-id="0567a-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="0567a-134">Klient gRPC będzie obsługiwać serializacji wiadomości i adresowania wywołania gRPC do poprawnej usługi.</span><span class="sxs-lookup"><span data-stu-id="0567a-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="0567a-135">gRPC ma różne rodzaje metod.</span><span class="sxs-lookup"><span data-stu-id="0567a-135">gRPC has different types of methods.</span></span> <span data-ttu-id="0567a-136">Sposób korzystania z klienta do wywołania gRPC zależy od typu metody, którą wywołujesz.</span><span class="sxs-lookup"><span data-stu-id="0567a-136">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="0567a-137">Typy metod gRPC to:</span><span class="sxs-lookup"><span data-stu-id="0567a-137">The gRPC method types are:</span></span>

* <span data-ttu-id="0567a-138">Jednoargumentowy</span><span class="sxs-lookup"><span data-stu-id="0567a-138">Unary</span></span>
* <span data-ttu-id="0567a-139">Przesyłanie strumieniowe serwera</span><span class="sxs-lookup"><span data-stu-id="0567a-139">Server streaming</span></span>
* <span data-ttu-id="0567a-140">Przesyłanie strumieniowe klienta</span><span class="sxs-lookup"><span data-stu-id="0567a-140">Client streaming</span></span>
* <span data-ttu-id="0567a-141">Dwukierunkowe przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="0567a-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="0567a-142">Połączenie bezemisowe</span><span class="sxs-lookup"><span data-stu-id="0567a-142">Unary call</span></span>

<span data-ttu-id="0567a-143">Połączenie bezemisowe rozpoczyna się od wysłania przez klienta wiadomości żądania.</span><span class="sxs-lookup"><span data-stu-id="0567a-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="0567a-144">Komunikat odpowiedzi jest zwracany po zakończeniu usługi.</span><span class="sxs-lookup"><span data-stu-id="0567a-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="0567a-145">Każda metoda usługi dwuarchifowej w pliku \* \*.proto\* spowoduje dwie metody .NET na typie klienta gRPC dla wywoływania metody: metoda asynchroniza i metoda blokowania.</span><span class="sxs-lookup"><span data-stu-id="0567a-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="0567a-146">Na przykład, `GreeterClient` na istnieją dwa `SayHello`sposoby wywoływania:</span><span class="sxs-lookup"><span data-stu-id="0567a-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="0567a-147">`GreeterClient.SayHelloAsync`- `Greeter.SayHello` wywołuje usługę asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="0567a-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="0567a-148">Można oczekiwać.</span><span class="sxs-lookup"><span data-stu-id="0567a-148">Can be awaited.</span></span>
* <span data-ttu-id="0567a-149">`GreeterClient.SayHello`- `Greeter.SayHello` dzwoni serwis i blokuje się do czasu zakończenia.</span><span class="sxs-lookup"><span data-stu-id="0567a-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="0567a-150">Nie używaj w kodzie asynchroniza.</span><span class="sxs-lookup"><span data-stu-id="0567a-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="0567a-151">Wywołanie przesyłania strumieniowego serwera</span><span class="sxs-lookup"><span data-stu-id="0567a-151">Server streaming call</span></span>

<span data-ttu-id="0567a-152">Wywołanie przesyłania strumieniowego serwera rozpoczyna się od wysłania przez klienta komunikatu żądania.</span><span class="sxs-lookup"><span data-stu-id="0567a-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="0567a-153">`ResponseStream.MoveNext()`odczytuje wiadomości przesyłane strumieniowo z usługi.</span><span class="sxs-lookup"><span data-stu-id="0567a-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="0567a-154">Połączenie przesyłania strumieniowego `ResponseStream.MoveNext()` serwera `false`zostanie zakończone po zwróceniu .</span><span class="sxs-lookup"><span data-stu-id="0567a-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

<span data-ttu-id="0567a-155">Jeśli używasz języka C# 8 `await foreach` lub nowszego, składni może służyć do odczytywania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="0567a-155">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="0567a-156">Metoda `IAsyncStreamReader<T>.ReadAllAsync()` rozszerzenia odczytuje wszystkie komunikaty ze strumienia odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="0567a-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="0567a-157">Połączenie strumieniowe klienta</span><span class="sxs-lookup"><span data-stu-id="0567a-157">Client streaming call</span></span>

<span data-ttu-id="0567a-158">Połączenie przesyłania strumieniowego klienta rozpoczyna się *bez* wysyłania wiadomości przez klienta.</span><span class="sxs-lookup"><span data-stu-id="0567a-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="0567a-159">Klient może wybrać opcję `RequestStream.WriteAsync`wysyłania wiadomości za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="0567a-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="0567a-160">Po zakończeniu wysyłania `RequestStream.CompleteAsync` wiadomości przez klienta należy wywołać, aby powiadomić usługę.</span><span class="sxs-lookup"><span data-stu-id="0567a-160">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="0567a-161">Wywołanie jest zakończone, gdy usługa zwraca komunikat odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="0567a-161">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="0567a-162">Dwukierunkowe połączenie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="0567a-162">Bi-directional streaming call</span></span>

<span data-ttu-id="0567a-163">Dwukierunkowe połączenie przesyłania strumieniowego rozpoczyna się *bez* wysyłania wiadomości przez klienta.</span><span class="sxs-lookup"><span data-stu-id="0567a-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="0567a-164">Klient może wybrać opcję `RequestStream.WriteAsync`wysyłania wiadomości za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="0567a-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="0567a-165">Wiadomości przesyłane strumieniowo z `ResponseStream.MoveNext()` `ResponseStream.ReadAllAsync()`usługi są dostępne za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="0567a-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="0567a-166">Dwukierunkowe wywołanie przesyłania strumieniowego `ResponseStream` jest zakończone, gdy nie ma więcej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="0567a-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
using (var call = client.Echo())
{
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
}
```

<span data-ttu-id="0567a-167">Podczas dwukierunkowego połączenia przesyłania strumieniowego klient i usługa mogą wysyłać do siebie wiadomości w dowolnym momencie.</span><span class="sxs-lookup"><span data-stu-id="0567a-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="0567a-168">Najlepsza logika klienta do interakcji z wywołaniem dwukierunkowym różni się w zależności od logiki usługi.</span><span class="sxs-lookup"><span data-stu-id="0567a-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0567a-169">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="0567a-169">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
