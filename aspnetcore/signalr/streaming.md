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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/streaming
ms.openlocfilehash: c7a3c7bb88230d84025bdf02deb98b51a2d1f92a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406176"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>Używanie przesyłania strumieniowego w ASP.NET CoreSignalR

Autor [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR obsługuje przesyłanie strumieniowe z klienta do serwera i z serwera do klienta. Jest to przydatne w scenariuszach, w których fragmenty danych docierają w czasie. Podczas przesyłania strumieniowego każdy fragment jest wysyłany do klienta lub serwera zaraz po jego udostępnieniu, a nie w celu uzyskania dostępu do wszystkich danych.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR obsługuje przesyłanie strumieniowe zwracanych wartości metod serwera. Jest to przydatne w scenariuszach, w których fragmenty danych docierają w czasie. Gdy wartość zwracana jest przesyłana strumieniowo do klienta, każdy fragment jest wysyłany do klienta natychmiast po jego udostępnieniu, a nie czeka na udostępnienie wszystkich danych.

::: moniker-end

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Konfigurowanie centrum do przesyłania strumieniowego

::: moniker range=">= aspnetcore-3.0"

Metoda centrum automatycznie zmienia metodę centrum przesyłania strumieniowego, gdy zwraca <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` , lub `Task<ChannelReader<T>>` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Metoda centrum automatycznie zmienia metodę centrum przesyłania strumieniowego, gdy zwraca <xref:System.Threading.Channels.ChannelReader%601> lub `Task<ChannelReader<T>>` .

::: moniker-end

### <a name="server-to-client-streaming"></a>Przesyłanie strumieniowe między serwerami i klientami

::: moniker range=">= aspnetcore-3.0"

Metody centrum przesyłania strumieniowego mogą `IAsyncEnumerable<T>` być dodatkowo zwracane do programu `ChannelReader<T>` . Najprostszym sposobem zwrócenia `IAsyncEnumerable<T>` jest utworzenie metody centrum jako metody asynchronicznej iteratora, jak pokazano w poniższym przykładzie. Metody iteratorów asynchronicznych centrum mogą akceptować `CancellationToken` Parametry wyzwalane, gdy klient anulował subskrypcję ze strumienia. Metody iteratorów asynchronicznych unikają typowych problemów z kanałami, takich jak nie zwracają `ChannelReader` wystarczająco wczesnych lub kończących metodę bez wykonywania operacji <xref:System.Threading.Channels.ChannelWriter`1> .

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Poniższy przykład przedstawia podstawy przesyłania strumieniowego danych do klienta przy użyciu kanałów. Za każdym razem, gdy obiekt jest zapisywana w <xref:System.Threading.Channels.ChannelWriter%601> , obiekt jest natychmiast wysyłany do klienta. Na koniec `ChannelWriter` zostanie zakończona, aby poinformować klienta, że strumień jest zamknięty.

> [!NOTE]
> Zapisz w `ChannelWriter<T>` wątku w tle i zwróć `ChannelReader` tak szybko, jak to możliwe. Inne wywołania centrów są blokowane do momentu `ChannelReader` zwrócenia.
>
> Zawijanie logiki w `try ... catch` . `Channel` `catch` `catch` Aby upewnić się, że wywołanie metody Hub zostało wykonane prawidłowo, Wypełnij w i poza nim.

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

Metody centrum przesyłania strumieniowego z serwera do klienta mogą akceptować `CancellationToken` Parametry wyzwalane, gdy klient anulował subskrypcję ze strumienia. Użyj tego tokenu, aby zatrzymać operację serwera i zwolnić wszystkie zasoby, jeśli klient odłączy się przed końcem strumienia.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Przesyłanie strumieniowe klient-serwer

Metoda centrum automatycznie zmienia metodę centrum przesyłania strumieniowego klienta na serwer, gdy akceptuje jeden lub więcej obiektów typu <xref:System.Threading.Channels.ChannelReader%601> lub <xref:System.Collections.Generic.IAsyncEnumerable%601> . W poniższym przykładzie przedstawiono podstawowe informacje dotyczące odczytywania danych przesyłanych strumieniowo z klienta programu. Za każdym razem, gdy klient zapisuje w programie <xref:System.Threading.Channels.ChannelWriter%601> , dane są zapisywane na `ChannelReader` serwerze, z którego jest odczytywana Metoda centrum.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<xref:System.Collections.Generic.IAsyncEnumerable%601>Poniżej znajduje się wersja metody.

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

## <a name="net-client"></a>Klient .NET

### <a name="server-to-client-streaming"></a>Przesyłanie strumieniowe między serwerami i klientami


::: moniker range=">= aspnetcore-3.0"

`StreamAsync`Metody i `StreamAsChannelAsync` `HubConnection` są używane do wywoływania metod przesyłania strumieniowego między serwerami i klientami. Przekaż nazwę i argumenty metody centrum zdefiniowane w metodzie Hub do `StreamAsync` lub `StreamAsChannelAsync` . Parametr generyczny dla `StreamAsync<T>` i `StreamAsChannelAsync<T>` określa typ obiektów zwracanych przez metodę przesyłania strumieniowego. Obiekt typu `IAsyncEnumerable<T>` lub `ChannelReader<T>` jest zwracany przez wywołanie strumienia i reprezentuje strumień na kliencie.

`StreamAsync`Przykład, który zwraca `IAsyncEnumerable<int>` :

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

Odpowiadający `StreamAsChannelAsync` przykład, który zwraca `ChannelReader<int>` :

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

`StreamAsChannelAsync`Metoda `HubConnection` jest używana do wywołania metody przesyłania strumieniowego między serwerami i klientami. Przekaż nazwę i argumenty metody centrum zdefiniowane w metodzie Hub do `StreamAsChannelAsync` . Parametr generyczny `StreamAsChannelAsync<T>` określa typ obiektów zwracanych przez metodę przesyłania strumieniowego. Element `ChannelReader<T>` jest zwracany przez wywołanie strumienia i reprezentuje strumień na kliencie.

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

`StreamAsChannelAsync`Metoda `HubConnection` jest używana do wywołania metody przesyłania strumieniowego między serwerami i klientami. Przekaż nazwę i argumenty metody centrum zdefiniowane w metodzie Hub do `StreamAsChannelAsync` . Parametr generyczny `StreamAsChannelAsync<T>` określa typ obiektów zwracanych przez metodę przesyłania strumieniowego. Element `ChannelReader<T>` jest zwracany przez wywołanie strumienia i reprezentuje strumień na kliencie.

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

### <a name="client-to-server-streaming"></a>Przesyłanie strumieniowe klient-serwer

Istnieją dwa sposoby wywołania metody centrum przesyłania strumieniowego klienta na serwer z poziomu klienta platformy .NET. Można przekazać parametr `IAsyncEnumerable<T>` lub a `ChannelReader` jako argument do `SendAsync` , `InvokeAsync` lub `StreamAsChannelAsync` , w zależności od wywoływanej metody centrum.

Za każdym razem, gdy dane są zapisywane w `IAsyncEnumerable` `ChannelWriter` obiekcie lub, Metoda Hub na serwerze otrzymuje nowy element z danymi od klienta.

W przypadku użycia `IAsyncEnumerable` obiektu strumień kończy się po metodzie zwracającej strumienia elementy.

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

Lub jeśli korzystasz z programu `ChannelWriter` , Ukończ kanał przy użyciu `channel.Writer.Complete()` :

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>Klient JavaScript

### <a name="server-to-client-streaming"></a>Przesyłanie strumieniowe między serwerami i klientami

Klienci języka JavaScript wywołują metody przesyłania strumieniowego z serwera do klienta w centrach za pomocą programu `connection.stream` . `stream`Metoda akceptuje dwa argumenty:

* Nazwa metody centrum. W poniższym przykładzie nazwa metody centrum to `Counter` .
* Argumenty zdefiniowane w metodzie centrum. W poniższym przykładzie argumenty są liczbami elementów strumienia do odebrania oraz opóźnieniem między elementami strumienia.

`connection.stream`Zwraca element `IStreamResult` , który zawiera `subscribe` metodę. Przekaż `IStreamSubscriber` do `subscribe` i ustaw `next` `error` `complete` wywołania zwrotne, i, aby otrzymywać powiadomienia z `stream` wywołania.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Aby zakończyć strumień z klienta, wywołaj `dispose` metodę `ISubscription` zwracaną z `subscribe` metody. Wywołanie tej metody powoduje anulowanie `CancellationToken` parametru metody centrum, jeśli został podany.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Aby zakończyć strumień z klienta, wywołaj `dispose` metodę `ISubscription` zwracaną z `subscribe` metody.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Przesyłanie strumieniowe klient-serwer

Klienci języka JavaScript wywołują metody przesyłania strumieniowego klient-serwer w centrach, przekazując `Subject` jako argument do `send` , `invoke` lub `stream` , w zależności od wywoływanej metody centrum. `Subject`Jest klasą, która wygląda jak `Subject` . Na przykład w RxJS można użyć klasy [subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) z tej biblioteki.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Wywołanie `subject.next(item)` elementu zapisuje element do strumienia, a metoda Hub odbiera element na serwerze.

Aby zakończyć strumień, wywołaj polecenie `subject.complete()` .

## <a name="java-client"></a>Klient Java

### <a name="server-to-client-streaming"></a>Przesyłanie strumieniowe między serwerami i klientami

SignalRKlient Java używa `stream` metody do wywoływania metod przesyłania strumieniowego. `stream`akceptuje trzy lub więcej argumentów:

* Oczekiwany typ elementów strumienia.
* Nazwa metody centrum.
* Argumenty zdefiniowane w metodzie centrum.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`stream`Metoda on `HubConnection` zwraca widoczny dla typu elementu strumienia. Metoda zauważalnego typu `subscribe` ma miejsce, gdzie `onNext` `onError` `onCompleted` są zdefiniowane i procedury obsługi.

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScript](xref:signalr/javascript-client)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
