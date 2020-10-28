---
title: 'Używanie przesyłania strumieniowego w ASP.NET Core :::no-loc(SignalR):::'
author: bradygaster
description: Dowiedz się, jak przesyłać strumieniowo dane między klientem a serwerem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 11/12/2019
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/streaming
ms.openlocfilehash: 732198cf07392bda008c9cc1c9768df2500852c0
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690638"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="c5515-103">Używanie przesyłania strumieniowego w ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="c5515-103">Use streaming in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="c5515-104">Autor [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="c5515-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5515-105">ASP.NET Core :::no-loc(SignalR)::: obsługuje przesyłanie strumieniowe z klienta do serwera i z serwera do klienta.</span><span class="sxs-lookup"><span data-stu-id="c5515-105">ASP.NET Core :::no-loc(SignalR)::: supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="c5515-106">Jest to przydatne w scenariuszach, w których fragmenty danych docierają w czasie.</span><span class="sxs-lookup"><span data-stu-id="c5515-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="c5515-107">Podczas przesyłania strumieniowego każdy fragment jest wysyłany do klienta lub serwera zaraz po jego udostępnieniu, a nie w celu uzyskania dostępu do wszystkich danych.</span><span class="sxs-lookup"><span data-stu-id="c5515-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c5515-108">ASP.NET Core :::no-loc(SignalR)::: obsługuje przesyłanie strumieniowe zwracanych wartości metod serwera.</span><span class="sxs-lookup"><span data-stu-id="c5515-108">ASP.NET Core :::no-loc(SignalR)::: supports streaming return values of server methods.</span></span> <span data-ttu-id="c5515-109">Jest to przydatne w scenariuszach, w których fragmenty danych docierają w czasie.</span><span class="sxs-lookup"><span data-stu-id="c5515-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="c5515-110">Gdy wartość zwracana jest przesyłana strumieniowo do klienta, każdy fragment jest wysyłany do klienta natychmiast po jego udostępnieniu, a nie czeka na udostępnienie wszystkich danych.</span><span class="sxs-lookup"><span data-stu-id="c5515-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="c5515-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c5515-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="c5515-112">Konfigurowanie centrum do przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="c5515-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5515-113">Metoda centrum automatycznie zmienia metodę centrum przesyłania strumieniowego, gdy zwraca <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` , lub `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="c5515-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c5515-114">Metoda centrum automatycznie zmienia metodę centrum przesyłania strumieniowego, gdy zwraca <xref:System.Threading.Channels.ChannelReader%601> lub `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="c5515-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="c5515-115">Przesyłanie strumieniowe między serwerami i klientami</span><span class="sxs-lookup"><span data-stu-id="c5515-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5515-116">Metody centrum przesyłania strumieniowego mogą `IAsyncEnumerable<T>` być dodatkowo zwracane do programu `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="c5515-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="c5515-117">Najprostszym sposobem zwrócenia `IAsyncEnumerable<T>` jest utworzenie metody centrum jako metody asynchronicznej iteratora, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="c5515-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="c5515-118">Metody iteratorów asynchronicznych centrum mogą akceptować `CancellationToken` Parametry wyzwalane, gdy klient anulował subskrypcję ze strumienia.</span><span class="sxs-lookup"><span data-stu-id="c5515-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="c5515-119">Metody iteratorów asynchronicznych unikają typowych problemów z kanałami, takich jak nie zwracają `ChannelReader` wystarczająco wczesnych lub kończących metodę bez wykonywania operacji <xref:System.Threading.Channels.ChannelWriter`1> .</span><span class="sxs-lookup"><span data-stu-id="c5515-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="c5515-120">Poniższy przykład przedstawia podstawy przesyłania strumieniowego danych do klienta przy użyciu kanałów.</span><span class="sxs-lookup"><span data-stu-id="c5515-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="c5515-121">Za każdym razem, gdy obiekt jest zapisywana w <xref:System.Threading.Channels.ChannelWriter%601> , obiekt jest natychmiast wysyłany do klienta.</span><span class="sxs-lookup"><span data-stu-id="c5515-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="c5515-122">Na koniec `ChannelWriter` zostanie zakończona, aby poinformować klienta, że strumień jest zamknięty.</span><span class="sxs-lookup"><span data-stu-id="c5515-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="c5515-123">Zapisz w `ChannelWriter<T>` wątku w tle i zwróć `ChannelReader` tak szybko, jak to możliwe.</span><span class="sxs-lookup"><span data-stu-id="c5515-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="c5515-124">Inne wywołania centrów są blokowane do momentu `ChannelReader` zwrócenia.</span><span class="sxs-lookup"><span data-stu-id="c5515-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="c5515-125">Zawijanie logiki w [ `try ... catch` instrukcji](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="c5515-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="c5515-126">Ukończ `Channel` w [ `finally` bloku](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span><span class="sxs-lookup"><span data-stu-id="c5515-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="c5515-127">Jeśli chcesz utworzyć przepływ błędu, Przechwyć go wewnątrz `catch` bloku i Zapisz go w `finally` bloku.</span><span class="sxs-lookup"><span data-stu-id="c5515-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

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

<span data-ttu-id="c5515-128">Metody centrum przesyłania strumieniowego z serwera do klienta mogą akceptować `CancellationToken` Parametry wyzwalane, gdy klient anulował subskrypcję ze strumienia.</span><span class="sxs-lookup"><span data-stu-id="c5515-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="c5515-129">Użyj tego tokenu, aby zatrzymać operację serwera i zwolnić wszystkie zasoby, jeśli klient odłączy się przed końcem strumienia.</span><span class="sxs-lookup"><span data-stu-id="c5515-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="c5515-130">Przesyłanie strumieniowe klient-serwer</span><span class="sxs-lookup"><span data-stu-id="c5515-130">Client-to-server streaming</span></span>

<span data-ttu-id="c5515-131">Metoda centrum automatycznie zmienia metodę centrum przesyłania strumieniowego klienta na serwer, gdy akceptuje jeden lub więcej obiektów typu <xref:System.Threading.Channels.ChannelReader%601> lub <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="c5515-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="c5515-132">W poniższym przykładzie przedstawiono podstawowe informacje dotyczące odczytywania danych przesyłanych strumieniowo z klienta programu.</span><span class="sxs-lookup"><span data-stu-id="c5515-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="c5515-133">Za każdym razem, gdy klient zapisuje w programie <xref:System.Threading.Channels.ChannelWriter%601> , dane są zapisywane na `ChannelReader` serwerze, z którego jest odczytywana Metoda centrum.</span><span class="sxs-lookup"><span data-stu-id="c5515-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="c5515-134"><xref:System.Collections.Generic.IAsyncEnumerable%601>Poniżej znajduje się wersja metody.</span><span class="sxs-lookup"><span data-stu-id="c5515-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

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

## <a name="net-client"></a><span data-ttu-id="c5515-135">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="c5515-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="c5515-136">Przesyłanie strumieniowe między serwerami i klientami</span><span class="sxs-lookup"><span data-stu-id="c5515-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5515-137">`StreamAsync`Metody i `StreamAsChannelAsync` `HubConnection` są używane do wywoływania metod przesyłania strumieniowego między serwerami i klientami.</span><span class="sxs-lookup"><span data-stu-id="c5515-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="c5515-138">Przekaż nazwę i argumenty metody centrum zdefiniowane w metodzie Hub do `StreamAsync` lub `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="c5515-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="c5515-139">Parametr generyczny dla `StreamAsync<T>` i `StreamAsChannelAsync<T>` określa typ obiektów zwracanych przez metodę przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="c5515-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="c5515-140">Obiekt typu `IAsyncEnumerable<T>` lub `ChannelReader<T>` jest zwracany przez wywołanie strumienia i reprezentuje strumień na kliencie.</span><span class="sxs-lookup"><span data-stu-id="c5515-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="c5515-141">`StreamAsync`Przykład, który zwraca `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="c5515-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="c5515-142">Odpowiadający `StreamAsChannelAsync` przykład, który zwraca `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="c5515-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="c5515-143">`StreamAsChannelAsync`Metoda `HubConnection` jest używana do wywołania metody przesyłania strumieniowego między serwerami i klientami.</span><span class="sxs-lookup"><span data-stu-id="c5515-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="c5515-144">Przekaż nazwę i argumenty metody centrum zdefiniowane w metodzie Hub do `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="c5515-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="c5515-145">Parametr generyczny `StreamAsChannelAsync<T>` określa typ obiektów zwracanych przez metodę przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="c5515-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="c5515-146">Element `ChannelReader<T>` jest zwracany przez wywołanie strumienia i reprezentuje strumień na kliencie.</span><span class="sxs-lookup"><span data-stu-id="c5515-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="c5515-147">`StreamAsChannelAsync`Metoda `HubConnection` jest używana do wywołania metody przesyłania strumieniowego między serwerami i klientami.</span><span class="sxs-lookup"><span data-stu-id="c5515-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="c5515-148">Przekaż nazwę i argumenty metody centrum zdefiniowane w metodzie Hub do `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="c5515-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="c5515-149">Parametr generyczny `StreamAsChannelAsync<T>` określa typ obiektów zwracanych przez metodę przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="c5515-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="c5515-150">Element `ChannelReader<T>` jest zwracany przez wywołanie strumienia i reprezentuje strumień na kliencie.</span><span class="sxs-lookup"><span data-stu-id="c5515-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="c5515-151">Przesyłanie strumieniowe klient-serwer</span><span class="sxs-lookup"><span data-stu-id="c5515-151">Client-to-server streaming</span></span>

<span data-ttu-id="c5515-152">Istnieją dwa sposoby wywołania metody centrum przesyłania strumieniowego klienta na serwer z poziomu klienta platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="c5515-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="c5515-153">Można przekazać parametr `IAsyncEnumerable<T>` lub a `ChannelReader` jako argument do `SendAsync` , `InvokeAsync` lub `StreamAsChannelAsync` , w zależności od wywoływanej metody centrum.</span><span class="sxs-lookup"><span data-stu-id="c5515-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="c5515-154">Za każdym razem, gdy dane są zapisywane w `IAsyncEnumerable` `ChannelWriter` obiekcie lub, Metoda Hub na serwerze otrzymuje nowy element z danymi od klienta.</span><span class="sxs-lookup"><span data-stu-id="c5515-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="c5515-155">W przypadku użycia `IAsyncEnumerable` obiektu strumień kończy się po metodzie zwracającej strumienia elementy.</span><span class="sxs-lookup"><span data-stu-id="c5515-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="c5515-156">Lub jeśli korzystasz z programu `ChannelWriter` , Ukończ kanał przy użyciu `channel.Writer.Complete()` :</span><span class="sxs-lookup"><span data-stu-id="c5515-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="c5515-157">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="c5515-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="c5515-158">Przesyłanie strumieniowe między serwerami i klientami</span><span class="sxs-lookup"><span data-stu-id="c5515-158">Server-to-client streaming</span></span>

<span data-ttu-id="c5515-159">Klienci języka JavaScript wywołują metody przesyłania strumieniowego z serwera do klienta w centrach za pomocą programu `connection.stream` .</span><span class="sxs-lookup"><span data-stu-id="c5515-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="c5515-160">`stream`Metoda akceptuje dwa argumenty:</span><span class="sxs-lookup"><span data-stu-id="c5515-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="c5515-161">Nazwa metody centrum.</span><span class="sxs-lookup"><span data-stu-id="c5515-161">The name of the hub method.</span></span> <span data-ttu-id="c5515-162">W poniższym przykładzie nazwa metody centrum to `Counter` .</span><span class="sxs-lookup"><span data-stu-id="c5515-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="c5515-163">Argumenty zdefiniowane w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="c5515-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="c5515-164">W poniższym przykładzie argumenty są liczbami elementów strumienia do odebrania oraz opóźnieniem między elementami strumienia.</span><span class="sxs-lookup"><span data-stu-id="c5515-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="c5515-165">`connection.stream` Zwraca element `IStreamResult` , który zawiera `subscribe` metodę.</span><span class="sxs-lookup"><span data-stu-id="c5515-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="c5515-166">Przekaż `IStreamSubscriber` do `subscribe` i ustaw `next` `error` `complete` wywołania zwrotne, i, aby otrzymywać powiadomienia z `stream` wywołania.</span><span class="sxs-lookup"><span data-stu-id="c5515-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="c5515-167">Aby zakończyć strumień z klienta, wywołaj `dispose` metodę `ISubscription` zwracaną z `subscribe` metody.</span><span class="sxs-lookup"><span data-stu-id="c5515-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="c5515-168">Wywołanie tej metody powoduje anulowanie `CancellationToken` parametru metody centrum, jeśli został podany.</span><span class="sxs-lookup"><span data-stu-id="c5515-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="c5515-169">Aby zakończyć strumień z klienta, wywołaj `dispose` metodę `ISubscription` zwracaną z `subscribe` metody.</span><span class="sxs-lookup"><span data-stu-id="c5515-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="c5515-170">Przesyłanie strumieniowe klient-serwer</span><span class="sxs-lookup"><span data-stu-id="c5515-170">Client-to-server streaming</span></span>

<span data-ttu-id="c5515-171">Klienci języka JavaScript wywołują metody przesyłania strumieniowego klient-serwer w centrach, przekazując `Subject` jako argument do `send` , `invoke` lub `stream` , w zależności od wywoływanej metody centrum.</span><span class="sxs-lookup"><span data-stu-id="c5515-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="c5515-172">`Subject`Jest klasą, która wygląda jak `Subject` .</span><span class="sxs-lookup"><span data-stu-id="c5515-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="c5515-173">Na przykład w RxJS można użyć klasy [subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) z tej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="c5515-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="c5515-174">Wywołanie `subject.next(item)` elementu zapisuje element do strumienia, a metoda Hub odbiera element na serwerze.</span><span class="sxs-lookup"><span data-stu-id="c5515-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="c5515-175">Aby zakończyć strumień, wywołaj polecenie `subject.complete()` .</span><span class="sxs-lookup"><span data-stu-id="c5515-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="c5515-176">Klient Java</span><span class="sxs-lookup"><span data-stu-id="c5515-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="c5515-177">Przesyłanie strumieniowe między serwerami i klientami</span><span class="sxs-lookup"><span data-stu-id="c5515-177">Server-to-client streaming</span></span>

<span data-ttu-id="c5515-178">:::no-loc(SignalR):::Klient Java używa `stream` metody do wywoływania metod przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="c5515-178">The :::no-loc(SignalR)::: Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="c5515-179">`stream` akceptuje trzy lub więcej argumentów:</span><span class="sxs-lookup"><span data-stu-id="c5515-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="c5515-180">Oczekiwany typ elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="c5515-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="c5515-181">Nazwa metody centrum.</span><span class="sxs-lookup"><span data-stu-id="c5515-181">The name of the hub method.</span></span>
* <span data-ttu-id="c5515-182">Argumenty zdefiniowane w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="c5515-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="c5515-183">`stream`Metoda on `HubConnection` zwraca widoczny dla typu elementu strumienia.</span><span class="sxs-lookup"><span data-stu-id="c5515-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="c5515-184">Metoda zauważalnego typu `subscribe` ma miejsce, gdzie `onNext` `onError` `onCompleted` są zdefiniowane i procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="c5515-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c5515-185">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c5515-185">Additional resources</span></span>

* [<span data-ttu-id="c5515-186">Centra</span><span class="sxs-lookup"><span data-stu-id="c5515-186">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c5515-187">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="c5515-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c5515-188">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="c5515-188">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="c5515-189">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="c5515-189">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
