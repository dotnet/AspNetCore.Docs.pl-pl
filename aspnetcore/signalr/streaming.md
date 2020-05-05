---
title: Używanie przesyłania strumieniowego w ASP.NET CoreSignalR
author: bradygaster
description: Dowiedz się, jak przesyłać strumieniowo dane między klientem a serwerem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/streaming
ms.openlocfilehash: 4d6461bc85573776ccdbe81bf3c74145a9cf7ed6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773893"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="68635-103">Używanie przesyłania strumieniowego w ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="68635-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="68635-104">Autor [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="68635-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68635-105">ASP.NET Core SignalR obsługuje przesyłanie strumieniowe z klienta do serwera i z serwera do klienta.</span><span class="sxs-lookup"><span data-stu-id="68635-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="68635-106">Jest to przydatne w scenariuszach, w których fragmenty danych docierają w czasie.</span><span class="sxs-lookup"><span data-stu-id="68635-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="68635-107">Podczas przesyłania strumieniowego każdy fragment jest wysyłany do klienta lub serwera zaraz po jego udostępnieniu, a nie w celu uzyskania dostępu do wszystkich danych.</span><span class="sxs-lookup"><span data-stu-id="68635-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68635-108">ASP.NET Core SignalR obsługuje przesyłanie strumieniowe zwracanych wartości metod serwera.</span><span class="sxs-lookup"><span data-stu-id="68635-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="68635-109">Jest to przydatne w scenariuszach, w których fragmenty danych docierają w czasie.</span><span class="sxs-lookup"><span data-stu-id="68635-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="68635-110">Gdy wartość zwracana jest przesyłana strumieniowo do klienta, każdy fragment jest wysyłany do klienta natychmiast po jego udostępnieniu, a nie czeka na udostępnienie wszystkich danych.</span><span class="sxs-lookup"><span data-stu-id="68635-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="68635-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68635-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="68635-112">Konfigurowanie centrum do przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="68635-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68635-113">Metoda centrum automatycznie zmienia metodę centrum przesyłania strumieniowego, gdy zwraca <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, lub `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="68635-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68635-114">Metoda centrum automatycznie zmienia metodę centrum przesyłania strumieniowego, gdy zwraca <xref:System.Threading.Channels.ChannelReader%601> lub. `Task<ChannelReader<T>>`</span><span class="sxs-lookup"><span data-stu-id="68635-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="68635-115">Przesyłanie strumieniowe między serwerami i klientami</span><span class="sxs-lookup"><span data-stu-id="68635-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68635-116">Metody centrum przesyłania strumieniowego `IAsyncEnumerable<T>` mogą być dodatkowo `ChannelReader<T>`zwracane do programu.</span><span class="sxs-lookup"><span data-stu-id="68635-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="68635-117">Najprostszym sposobem zwrócenia `IAsyncEnumerable<T>` jest utworzenie metody centrum jako metody asynchronicznej iteratora, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="68635-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="68635-118">Metody iteratorów asynchronicznych centrum mogą `CancellationToken` akceptować parametry wyzwalane, gdy klient anulował subskrypcję ze strumienia.</span><span class="sxs-lookup"><span data-stu-id="68635-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="68635-119">Metody iteratorów asynchronicznych unikają typowych problemów z kanałami, takich `ChannelReader` jak nie zwracają wystarczająco wczesnych lub kończących <xref:System.Threading.Channels.ChannelWriter`1>metodę bez wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="68635-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="68635-120">Poniższy przykład przedstawia podstawy przesyłania strumieniowego danych do klienta przy użyciu kanałów.</span><span class="sxs-lookup"><span data-stu-id="68635-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="68635-121">Za każdym razem <xref:System.Threading.Channels.ChannelWriter%601>, gdy obiekt jest zapisywana w, obiekt jest natychmiast wysyłany do klienta.</span><span class="sxs-lookup"><span data-stu-id="68635-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="68635-122">Na koniec `ChannelWriter` zostanie zakończona, aby poinformować klienta, że strumień jest zamknięty.</span><span class="sxs-lookup"><span data-stu-id="68635-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="68635-123">Zapisz w wątku `ChannelWriter<T>` w tle i zwróć `ChannelReader` tak szybko, jak to możliwe.</span><span class="sxs-lookup"><span data-stu-id="68635-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="68635-124">Inne wywołania centrów są blokowane do momentu `ChannelReader` zwrócenia.</span><span class="sxs-lookup"><span data-stu-id="68635-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="68635-125">Zawijanie logiki w `try ... catch`.</span><span class="sxs-lookup"><span data-stu-id="68635-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="68635-126">Aby upewnić się `catch` , że wywołanie `catch` metody Hub zostało wykonane prawidłowo, Wypełnij `Channel` w i poza nim.</span><span class="sxs-lookup"><span data-stu-id="68635-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="68635-127">Metody centrum przesyłania strumieniowego z serwera do klienta mogą akceptować `CancellationToken` parametry wyzwalane, gdy klient anulował subskrypcję ze strumienia.</span><span class="sxs-lookup"><span data-stu-id="68635-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="68635-128">Użyj tego tokenu, aby zatrzymać operację serwera i zwolnić wszystkie zasoby, jeśli klient odłączy się przed końcem strumienia.</span><span class="sxs-lookup"><span data-stu-id="68635-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="68635-129">Przesyłanie strumieniowe klient-serwer</span><span class="sxs-lookup"><span data-stu-id="68635-129">Client-to-server streaming</span></span>

<span data-ttu-id="68635-130">Metoda centrum automatycznie zmienia metodę centrum przesyłania strumieniowego klienta na serwer, gdy akceptuje jeden lub więcej obiektów typu <xref:System.Threading.Channels.ChannelReader%601> lub. <xref:System.Collections.Generic.IAsyncEnumerable%601></span><span class="sxs-lookup"><span data-stu-id="68635-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="68635-131">W poniższym przykładzie przedstawiono podstawowe informacje dotyczące odczytywania danych przesyłanych strumieniowo z klienta programu.</span><span class="sxs-lookup"><span data-stu-id="68635-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="68635-132">Za każdym razem <xref:System.Threading.Channels.ChannelWriter%601>, gdy klient zapisuje w programie, dane są zapisywane `ChannelReader` na serwerze, z którego jest odczytywana Metoda centrum.</span><span class="sxs-lookup"><span data-stu-id="68635-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="68635-133">Poniżej <xref:System.Collections.Generic.IAsyncEnumerable%601> znajduje się wersja metody.</span><span class="sxs-lookup"><span data-stu-id="68635-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="68635-134">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="68635-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="68635-135">Przesyłanie strumieniowe między serwerami i klientami</span><span class="sxs-lookup"><span data-stu-id="68635-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68635-136">Metody `StreamAsync` i `StreamAsChannelAsync` `HubConnection` są używane do wywoływania metod przesyłania strumieniowego między serwerami i klientami.</span><span class="sxs-lookup"><span data-stu-id="68635-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="68635-137">Przekaż nazwę i argumenty metody centrum zdefiniowane w metodzie Hub do `StreamAsync` lub. `StreamAsChannelAsync`</span><span class="sxs-lookup"><span data-stu-id="68635-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="68635-138">Parametr generyczny dla `StreamAsync<T>` i `StreamAsChannelAsync<T>` określa typ obiektów zwracanych przez metodę przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="68635-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="68635-139">Obiekt typu `IAsyncEnumerable<T>` lub `ChannelReader<T>` jest zwracany przez wywołanie strumienia i reprezentuje strumień na kliencie.</span><span class="sxs-lookup"><span data-stu-id="68635-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="68635-140">`StreamAsync` Przykład, który zwraca `IAsyncEnumerable<int>`:</span><span class="sxs-lookup"><span data-stu-id="68635-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="68635-141">Odpowiadający `StreamAsChannelAsync` przykład, który `ChannelReader<int>`zwraca:</span><span class="sxs-lookup"><span data-stu-id="68635-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="68635-142">`StreamAsChannelAsync` Metoda `HubConnection` jest używana do wywołania metody przesyłania strumieniowego między serwerami i klientami.</span><span class="sxs-lookup"><span data-stu-id="68635-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="68635-143">Przekaż nazwę i argumenty metody centrum zdefiniowane w metodzie Hub do `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="68635-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="68635-144">Parametr generyczny `StreamAsChannelAsync<T>` określa typ obiektów zwracanych przez metodę przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="68635-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="68635-145">Element `ChannelReader<T>` jest zwracany przez wywołanie strumienia i reprezentuje strumień na kliencie.</span><span class="sxs-lookup"><span data-stu-id="68635-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="68635-146">`StreamAsChannelAsync` Metoda `HubConnection` jest używana do wywołania metody przesyłania strumieniowego między serwerami i klientami.</span><span class="sxs-lookup"><span data-stu-id="68635-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="68635-147">Przekaż nazwę i argumenty metody centrum zdefiniowane w metodzie Hub do `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="68635-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="68635-148">Parametr generyczny `StreamAsChannelAsync<T>` określa typ obiektów zwracanych przez metodę przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="68635-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="68635-149">Element `ChannelReader<T>` jest zwracany przez wywołanie strumienia i reprezentuje strumień na kliencie.</span><span class="sxs-lookup"><span data-stu-id="68635-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="68635-150">Przesyłanie strumieniowe klient-serwer</span><span class="sxs-lookup"><span data-stu-id="68635-150">Client-to-server streaming</span></span>

<span data-ttu-id="68635-151">Istnieją dwa sposoby wywołania metody centrum przesyłania strumieniowego klienta na serwer z poziomu klienta platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="68635-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="68635-152">Można przekazać `IAsyncEnumerable<T>` parametr lub a `ChannelReader` jako argument do `SendAsync`, `InvokeAsync`lub `StreamAsChannelAsync`, w zależności od wywoływanej metody centrum.</span><span class="sxs-lookup"><span data-stu-id="68635-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="68635-153">Za każdym razem, gdy dane `IAsyncEnumerable` są `ChannelWriter` zapisywane w obiekcie lub, Metoda Hub na serwerze otrzymuje nowy element z danymi od klienta.</span><span class="sxs-lookup"><span data-stu-id="68635-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="68635-154">W przypadku użycia `IAsyncEnumerable` obiektu strumień kończy się po metodzie zwracającej strumienia elementy.</span><span class="sxs-lookup"><span data-stu-id="68635-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="68635-155">Lub jeśli korzystasz z programu `ChannelWriter`, Ukończ kanał przy użyciu `channel.Writer.Complete()`:</span><span class="sxs-lookup"><span data-stu-id="68635-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="68635-156">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="68635-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="68635-157">Przesyłanie strumieniowe między serwerami i klientami</span><span class="sxs-lookup"><span data-stu-id="68635-157">Server-to-client streaming</span></span>

<span data-ttu-id="68635-158">Klienci języka JavaScript wywołują metody przesyłania strumieniowego z serwera do klienta `connection.stream`w centrach za pomocą programu.</span><span class="sxs-lookup"><span data-stu-id="68635-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="68635-159">`stream` Metoda akceptuje dwa argumenty:</span><span class="sxs-lookup"><span data-stu-id="68635-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="68635-160">Nazwa metody centrum.</span><span class="sxs-lookup"><span data-stu-id="68635-160">The name of the hub method.</span></span> <span data-ttu-id="68635-161">W poniższym przykładzie nazwa metody centrum to `Counter`.</span><span class="sxs-lookup"><span data-stu-id="68635-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="68635-162">Argumenty zdefiniowane w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="68635-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="68635-163">W poniższym przykładzie argumenty są liczbami elementów strumienia do odebrania oraz opóźnieniem między elementami strumienia.</span><span class="sxs-lookup"><span data-stu-id="68635-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="68635-164">`connection.stream`Zwraca element `IStreamResult`, który zawiera `subscribe` metodę.</span><span class="sxs-lookup"><span data-stu-id="68635-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="68635-165">Przekaż `IStreamSubscriber` do `subscribe` `next`i ustaw wywołania zwrotne `error`, i `complete` , aby otrzymywać powiadomienia z `stream` wywołania.</span><span class="sxs-lookup"><span data-stu-id="68635-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="68635-166">Aby zakończyć strumień z klienta, wywołaj `dispose` metodę `ISubscription` zwracaną z `subscribe` metody.</span><span class="sxs-lookup"><span data-stu-id="68635-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="68635-167">Wywołanie tej metody powoduje anulowanie `CancellationToken` parametru metody centrum, jeśli został podany.</span><span class="sxs-lookup"><span data-stu-id="68635-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="68635-168">Aby zakończyć strumień z klienta, wywołaj `dispose` metodę `ISubscription` zwracaną z `subscribe` metody.</span><span class="sxs-lookup"><span data-stu-id="68635-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="68635-169">Przesyłanie strumieniowe klient-serwer</span><span class="sxs-lookup"><span data-stu-id="68635-169">Client-to-server streaming</span></span>

<span data-ttu-id="68635-170">Klienci języka JavaScript wywołują metody przesyłania strumieniowego klient-serwer w centrach, `Subject` przekazując jako argument do `send`, `invoke`lub `stream`, w zależności od wywoływanej metody centrum.</span><span class="sxs-lookup"><span data-stu-id="68635-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="68635-171">`Subject` Jest klasą, która wygląda jak `Subject`.</span><span class="sxs-lookup"><span data-stu-id="68635-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="68635-172">Na przykład w RxJS można użyć klasy [subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) z tej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="68635-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="68635-173">Wywołanie `subject.next(item)` elementu zapisuje element do strumienia, a metoda Hub odbiera element na serwerze.</span><span class="sxs-lookup"><span data-stu-id="68635-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="68635-174">Aby zakończyć strumień, wywołaj `subject.complete()`polecenie.</span><span class="sxs-lookup"><span data-stu-id="68635-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="68635-175">Klient Java</span><span class="sxs-lookup"><span data-stu-id="68635-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="68635-176">Przesyłanie strumieniowe między serwerami i klientami</span><span class="sxs-lookup"><span data-stu-id="68635-176">Server-to-client streaming</span></span>

<span data-ttu-id="68635-177">Klient SignalR Java używa `stream` metody do wywoływania metod przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="68635-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="68635-178">`stream`akceptuje trzy lub więcej argumentów:</span><span class="sxs-lookup"><span data-stu-id="68635-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="68635-179">Oczekiwany typ elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="68635-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="68635-180">Nazwa metody centrum.</span><span class="sxs-lookup"><span data-stu-id="68635-180">The name of the hub method.</span></span>
* <span data-ttu-id="68635-181">Argumenty zdefiniowane w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="68635-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="68635-182">`stream` Metoda on `HubConnection` zwraca widoczny dla typu elementu strumienia.</span><span class="sxs-lookup"><span data-stu-id="68635-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="68635-183">`subscribe` Metoda zauważalnego typu ma miejsce, `onNext` `onError` gdzie są zdefiniowane `onCompleted` i procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="68635-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="68635-184">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="68635-184">Additional resources</span></span>

* [<span data-ttu-id="68635-185">Centra</span><span class="sxs-lookup"><span data-stu-id="68635-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="68635-186">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="68635-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="68635-187">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="68635-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="68635-188">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="68635-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
