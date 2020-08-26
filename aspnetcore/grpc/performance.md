---
title: Najlepsze rozwiązania w zakresie wydajności
author: jamesnk
description: Poznaj najlepsze rozwiązania dotyczące tworzenia usług gRPC o wysokiej wydajności.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
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
uid: grpc/performance
ms.openlocfilehash: c6f6a9e5c9aa2f01209c8457a848dc6ec1f5ed88
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88866190"
---
# <a name="performance-best-practices"></a><span data-ttu-id="cb523-103">Najlepsze rozwiązania w zakresie wydajności</span><span class="sxs-lookup"><span data-stu-id="cb523-103">Performance best practices</span></span>

<span data-ttu-id="cb523-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="cb523-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="cb523-105">gRPC jest przeznaczony dla usług o wysokiej wydajności.</span><span class="sxs-lookup"><span data-stu-id="cb523-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="cb523-106">W tym dokumencie wyjaśniono, jak uzyskać najlepszą wydajność z gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb523-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="cb523-107">Użyj ponownie kanału</span><span class="sxs-lookup"><span data-stu-id="cb523-107">Reuse channel</span></span>

<span data-ttu-id="cb523-108">Podczas wykonywania wywołań gRPC należy ponownie użyć kanału gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb523-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="cb523-109">Ponowne użycie kanału umożliwia wywoływanie wywołań przez istniejące połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="cb523-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="cb523-110">Jeśli dla każdego wywołania gRPC zostanie utworzony nowy kanał, ilość czasu, jaką zajmie ukończenie, może znacząco wzrosnąć.</span><span class="sxs-lookup"><span data-stu-id="cb523-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="cb523-111">Każde wywołanie będzie wymagało wielu operacji sieciowych między klientem a serwerem w celu utworzenia połączenia HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="cb523-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="cb523-112">Otwieranie gniazda</span><span class="sxs-lookup"><span data-stu-id="cb523-112">Opening a socket</span></span>
2. <span data-ttu-id="cb523-113">Nawiązywanie połączenia TCP</span><span class="sxs-lookup"><span data-stu-id="cb523-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="cb523-114">Negocjowanie protokołu TLS</span><span class="sxs-lookup"><span data-stu-id="cb523-114">Negotiating TLS</span></span>
4. <span data-ttu-id="cb523-115">Uruchamianie połączenia HTTP/2</span><span class="sxs-lookup"><span data-stu-id="cb523-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="cb523-116">Wykonywanie wywołania gRPC</span><span class="sxs-lookup"><span data-stu-id="cb523-116">Making the gRPC call</span></span>

<span data-ttu-id="cb523-117">Kanały można bezpiecznie udostępniać i ponownie używać między gRPC wywołań:</span><span class="sxs-lookup"><span data-stu-id="cb523-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="cb523-118">gRPC klienci są tworzone za pomocą kanałów.</span><span class="sxs-lookup"><span data-stu-id="cb523-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="cb523-119">gRPC klienci są obiektami lekkimi i nie muszą być buforowane ani ponownie używane.</span><span class="sxs-lookup"><span data-stu-id="cb523-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="cb523-120">Wielu klientów gRPC można utworzyć na podstawie kanału, w tym różnych typów klientów.</span><span class="sxs-lookup"><span data-stu-id="cb523-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="cb523-121">Kanał i klienci utworzeni z kanału mogą być bezpiecznie używani przez wiele wątków.</span><span class="sxs-lookup"><span data-stu-id="cb523-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="cb523-122">Klienci utworzeni z kanału mogą wykonywać wiele jednoczesnych wywołań.</span><span class="sxs-lookup"><span data-stu-id="cb523-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="cb523-123">Współbieżność połączenia</span><span class="sxs-lookup"><span data-stu-id="cb523-123">Connection concurrency</span></span>

<span data-ttu-id="cb523-124">Połączenia HTTP/2 zwykle mają limit [maksymalnej liczby równoczesnych strumieni (aktywne żądania HTTP)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb523-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="cb523-125">Domyślnie większość serwerów ustawia ten limit na 100 strumieni współbieżnych.</span><span class="sxs-lookup"><span data-stu-id="cb523-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="cb523-126">Kanał gRPC korzysta z pojedynczego połączenia HTTP/2, a współbieżne wywołania są multipleksne dla tego połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb523-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="cb523-127">Gdy liczba aktywnych wywołań osiągnie limit strumienia połączeń, dodatkowe wywołania są umieszczane w kolejce na kliencie.</span><span class="sxs-lookup"><span data-stu-id="cb523-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="cb523-128">Wywołania w kolejce oczekują na zakończenie aktywnych wywołań przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="cb523-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="cb523-129">Aplikacje o dużym obciążeniu lub długotrwałych wywołaniach gRPC mogą zobaczyć problemy z wydajnością spowodowane przez wywołania kolejkowania z powodu tego limitu.</span><span class="sxs-lookup"><span data-stu-id="cb523-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cb523-130">W programie .NET 5 wprowadzono `SocketsHttpHandler.EnableMultipleHttp2Connections` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="cb523-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="cb523-131">W przypadku ustawienia opcji `true` dodatkowe połączenia HTTP/2 są tworzone przez kanał po osiągnięciu współbieżnego limitu strumienia.</span><span class="sxs-lookup"><span data-stu-id="cb523-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="cb523-132">Gdy `GrpcChannel` jest tworzony, jego wewnętrzny `SocketsHttpHandler` jest automatycznie konfigurowany do tworzenia dodatkowych połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="cb523-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="cb523-133">Jeśli aplikacja skonfiguruje własną obsługę, należy rozważyć ustawienie `EnableMultipleHttp2Connections` `true` :</span><span class="sxs-lookup"><span data-stu-id="cb523-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="cb523-134">Istnieją kilka obejść dla aplikacji platformy .NET Core 3,1:</span><span class="sxs-lookup"><span data-stu-id="cb523-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="cb523-135">Utwórz oddzielne kanały gRPC dla obszarów aplikacji o dużym obciążeniu.</span><span class="sxs-lookup"><span data-stu-id="cb523-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="cb523-136">Na przykład `Logger` Usługa gRPC może mieć duże obciążenie.</span><span class="sxs-lookup"><span data-stu-id="cb523-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="cb523-137">Aby utworzyć `LoggerClient` aplikację w aplikacji, użyj oddzielnego kanału.</span><span class="sxs-lookup"><span data-stu-id="cb523-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="cb523-138">Użyj puli kanałów gRPC, na przykład utwórz listę kanałów gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb523-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="cb523-139">`Random` służy do wybierania kanału z listy za każdym razem, gdy wymagany jest kanał gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb523-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="cb523-140">Używanie `Random` losowo dystrybuowanych wywołań przez wiele połączeń.</span><span class="sxs-lookup"><span data-stu-id="cb523-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cb523-141">Zwiększenie maksymalnego limitu współbieżności strumienia na serwerze jest innym sposobem rozwiązania tego problemu.</span><span class="sxs-lookup"><span data-stu-id="cb523-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="cb523-142">W Kestrel jest to konfiguracja przy użyciu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="cb523-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="cb523-143">Zwiększenie maksymalnego limitu współbieżności strumienia nie jest zalecane.</span><span class="sxs-lookup"><span data-stu-id="cb523-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="cb523-144">Zbyt wiele strumieni na pojedynczym połączeniu HTTP/2 wprowadza nowe problemy z wydajnością:</span><span class="sxs-lookup"><span data-stu-id="cb523-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="cb523-145">Rywalizacja o wątki między strumieniami próbującymi wykonać zapis w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="cb523-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="cb523-146">Utrata pakietów połączenia powoduje, że wszystkie wywołania są blokowane w warstwie TCP.</span><span class="sxs-lookup"><span data-stu-id="cb523-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="cb523-147">Utrzymywanie aktywności poleceń ping</span><span class="sxs-lookup"><span data-stu-id="cb523-147">Keep alive pings</span></span>

<span data-ttu-id="cb523-148">Polecenia ping utrzymywania aktywności mogą służyć do utrzymywania połączeń HTTP/2 w okresach braku aktywności.</span><span class="sxs-lookup"><span data-stu-id="cb523-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="cb523-149">Istniejące połączenie HTTP/2 jest gotowe, gdy aplikacja wznawia działanie, umożliwia szybkie wywoływanie gRPC, bez opóźnień spowodowanych przez ponowne nawiązanie połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb523-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="cb523-150">Monitoruj polecenia ping dla <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="cb523-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="cb523-151">Poprzedni kod konfiguruje kanał, który wysyła polecenie ping Keep Alive do serwera co 60 sekund w okresach braku aktywności.</span><span class="sxs-lookup"><span data-stu-id="cb523-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="cb523-152">Polecenie ping gwarantuje, że serwer i wszystkie używane serwery proxy nie będą zamykać połączenia z powodu braku aktywności.</span><span class="sxs-lookup"><span data-stu-id="cb523-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="cb523-153">Przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="cb523-153">Streaming</span></span>

<span data-ttu-id="cb523-154">gRPC dwukierunkowe przesyłanie strumieniowe może służyć do zastępowania jednoargumentowych wywołań gRPC w scenariuszach o wysokiej wydajności.</span><span class="sxs-lookup"><span data-stu-id="cb523-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="cb523-155">Po rozpoczęciu strumienia dwukierunkowego komunikaty przesyłające strumieniowo i z powrotem są szybsze niż wysyłanie komunikatów z wieloma jednoargumentowymi wywołaniami gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb523-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="cb523-156">Komunikaty przesyłane strumieniowo są wysyłane jako dane na istniejącym żądaniu HTTP/2 i eliminuje obciążenie związane z tworzeniem nowego żądania HTTP/2 dla każdego wywołania jednoargumentowego.</span><span class="sxs-lookup"><span data-stu-id="cb523-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="cb523-157">Przykładowa usługa:</span><span class="sxs-lookup"><span data-stu-id="cb523-157">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="cb523-158">Przykładowy klient:</span><span class="sxs-lookup"><span data-stu-id="cb523-158">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="cb523-159">Zamiana wywołań jednoargumentowych z dwukierunkowym przesyłaniem strumieniowym ze względu na wydajność jest zaawansowaną techniką i nie jest odpowiednia w wielu sytuacjach</span><span class="sxs-lookup"><span data-stu-id="cb523-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="cb523-160">Używanie wywołań przesyłania strumieniowego jest dobrym rozwiązaniem w przypadku:</span><span class="sxs-lookup"><span data-stu-id="cb523-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="cb523-161">Wymagana jest Wysoka przepływność lub małe opóźnienia.</span><span class="sxs-lookup"><span data-stu-id="cb523-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="cb523-162">gRPC i HTTP/2 są identyfikowane jako wąskie gardła wydajności.</span><span class="sxs-lookup"><span data-stu-id="cb523-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="cb523-163">Proces roboczy w kliencie wysyła lub otrzymuje zwykłe wiadomości za pomocą usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb523-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="cb523-164">Należy pamiętać o dodatkowej złożoności i ograniczeniach użycia wywołań przesyłania strumieniowego zamiast jednoargumentowego:</span><span class="sxs-lookup"><span data-stu-id="cb523-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="cb523-165">Strumień może zostać przerwany przez usługę lub błąd połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb523-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="cb523-166">Jeśli wystąpi błąd, logika jest wymagana do ponownego uruchomienia strumienia.</span><span class="sxs-lookup"><span data-stu-id="cb523-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="cb523-167">`RequestStream.WriteAsync` nie jest bezpieczna w przypadku wielowątkowości.</span><span class="sxs-lookup"><span data-stu-id="cb523-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="cb523-168">W danym momencie może być zapisany tylko jeden komunikat w strumieniu.</span><span class="sxs-lookup"><span data-stu-id="cb523-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="cb523-169">Wysyłanie komunikatów z wielu wątków za pośrednictwem pojedynczego strumienia wymaga kolejki producent/odbiorca, która jest taka sama jak <xref:System.Threading.Channels.Channel%601> w przypadku komunikatów.</span><span class="sxs-lookup"><span data-stu-id="cb523-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="cb523-170">Metoda przesyłania strumieniowego gRPC jest ograniczona do odebrania jednego typu komunikatu i wysłania jednego typu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="cb523-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="cb523-171">Na przykład `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` odbiera `RequestMessage` i wysyła `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="cb523-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="cb523-172">Protobuf obsługę komunikatów nieznanych lub warunkowych przy użyciu `Any` i może obejść `oneof` to ograniczenie.</span><span class="sxs-lookup"><span data-stu-id="cb523-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
