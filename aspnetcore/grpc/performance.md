---
title: Najlepsze rozwiązania w zakresie wydajności z gRPC
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
ms.openlocfilehash: b54d13bfd9207a7b8961c1c4fa9908d3d54a4270
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113858"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="9168b-103">Najlepsze rozwiązania w zakresie wydajności z gRPC</span><span class="sxs-lookup"><span data-stu-id="9168b-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="9168b-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9168b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9168b-105">gRPC jest przeznaczony dla usług o wysokiej wydajności.</span><span class="sxs-lookup"><span data-stu-id="9168b-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="9168b-106">W tym dokumencie wyjaśniono, jak uzyskać najlepszą wydajność z gRPC.</span><span class="sxs-lookup"><span data-stu-id="9168b-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="9168b-107">Ponowne używanie kanałów gRPC</span><span class="sxs-lookup"><span data-stu-id="9168b-107">Reuse gRPC channels</span></span>

<span data-ttu-id="9168b-108">Podczas wykonywania wywołań gRPC należy ponownie użyć kanału gRPC.</span><span class="sxs-lookup"><span data-stu-id="9168b-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="9168b-109">Ponowne użycie kanału umożliwia wywoływanie wywołań przez istniejące połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9168b-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="9168b-110">Jeśli dla każdego wywołania gRPC zostanie utworzony nowy kanał, ilość czasu, jaką zajmie ukończenie, może znacząco wzrosnąć.</span><span class="sxs-lookup"><span data-stu-id="9168b-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="9168b-111">Każde wywołanie będzie wymagało wielu operacji sieciowych między klientem a serwerem w celu utworzenia nowego połączenia HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="9168b-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="9168b-112">Otwieranie gniazda</span><span class="sxs-lookup"><span data-stu-id="9168b-112">Opening a socket</span></span>
2. <span data-ttu-id="9168b-113">Nawiązywanie połączenia TCP</span><span class="sxs-lookup"><span data-stu-id="9168b-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="9168b-114">Negocjowanie protokołu TLS</span><span class="sxs-lookup"><span data-stu-id="9168b-114">Negotiating TLS</span></span>
4. <span data-ttu-id="9168b-115">Uruchamianie połączenia HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9168b-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="9168b-116">Wykonywanie wywołania gRPC</span><span class="sxs-lookup"><span data-stu-id="9168b-116">Making the gRPC call</span></span>

<span data-ttu-id="9168b-117">Kanały można bezpiecznie udostępniać i ponownie używać między gRPC wywołań:</span><span class="sxs-lookup"><span data-stu-id="9168b-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="9168b-118">gRPC klienci są tworzone za pomocą kanałów.</span><span class="sxs-lookup"><span data-stu-id="9168b-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="9168b-119">gRPC klienci są obiektami lekkimi i nie muszą być buforowane ani ponownie używane.</span><span class="sxs-lookup"><span data-stu-id="9168b-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="9168b-120">Wielu klientów gRPC można utworzyć na podstawie kanału, w tym różnych typów klientów.</span><span class="sxs-lookup"><span data-stu-id="9168b-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="9168b-121">Kanał i klienci utworzeni z kanału mogą być bezpiecznie używani przez wiele wątków.</span><span class="sxs-lookup"><span data-stu-id="9168b-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="9168b-122">Klienci utworzeni z kanału mogą wykonywać wiele jednoczesnych wywołań.</span><span class="sxs-lookup"><span data-stu-id="9168b-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="9168b-123">Fabryka klientów gRPC oferuje scentralizowany sposób konfigurowania kanałów.</span><span class="sxs-lookup"><span data-stu-id="9168b-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="9168b-124">Automatycznie ponownie używa kanałów bazowych.</span><span class="sxs-lookup"><span data-stu-id="9168b-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="9168b-125">Aby uzyskać więcej informacji, zobacz <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="9168b-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="9168b-126">Współbieżność połączenia</span><span class="sxs-lookup"><span data-stu-id="9168b-126">Connection concurrency</span></span>

<span data-ttu-id="9168b-127">Połączenia HTTP/2 zwykle mają limit [maksymalnej liczby równoczesnych strumieni (aktywne żądania HTTP)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="9168b-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="9168b-128">Domyślnie większość serwerów ustawia ten limit na 100 strumieni współbieżnych.</span><span class="sxs-lookup"><span data-stu-id="9168b-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="9168b-129">Kanał gRPC korzysta z pojedynczego połączenia HTTP/2, a współbieżne wywołania są multipleksne dla tego połączenia.</span><span class="sxs-lookup"><span data-stu-id="9168b-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="9168b-130">Gdy liczba aktywnych wywołań osiągnie limit strumienia połączeń, dodatkowe wywołania są umieszczane w kolejce na kliencie.</span><span class="sxs-lookup"><span data-stu-id="9168b-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="9168b-131">Wywołania w kolejce oczekują na zakończenie aktywnych wywołań przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="9168b-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="9168b-132">Aplikacje o dużym obciążeniu lub długotrwałych wywołaniach gRPC mogą zobaczyć problemy z wydajnością spowodowane przez wywołania kolejkowania z powodu tego limitu.</span><span class="sxs-lookup"><span data-stu-id="9168b-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9168b-133">W programie .NET 5 wprowadzono `SocketsHttpHandler.EnableMultipleHttp2Connections` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="9168b-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="9168b-134">W przypadku ustawienia opcji `true` dodatkowe połączenia HTTP/2 są tworzone przez kanał po osiągnięciu współbieżnego limitu strumienia.</span><span class="sxs-lookup"><span data-stu-id="9168b-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="9168b-135">Gdy `GrpcChannel` jest tworzony, jego wewnętrzny `SocketsHttpHandler` jest automatycznie konfigurowany do tworzenia dodatkowych połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9168b-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="9168b-136">Jeśli aplikacja skonfiguruje własną obsługę, należy rozważyć ustawienie `EnableMultipleHttp2Connections` `true` :</span><span class="sxs-lookup"><span data-stu-id="9168b-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="9168b-137">Istnieją kilka obejść dla aplikacji platformy .NET Core 3,1:</span><span class="sxs-lookup"><span data-stu-id="9168b-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="9168b-138">Utwórz oddzielne kanały gRPC dla obszarów aplikacji o dużym obciążeniu.</span><span class="sxs-lookup"><span data-stu-id="9168b-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="9168b-139">Na przykład `Logger` Usługa gRPC może mieć duże obciążenie.</span><span class="sxs-lookup"><span data-stu-id="9168b-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="9168b-140">Aby utworzyć `LoggerClient` aplikację w aplikacji, użyj oddzielnego kanału.</span><span class="sxs-lookup"><span data-stu-id="9168b-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="9168b-141">Użyj puli kanałów gRPC, na przykład utwórz listę kanałów gRPC.</span><span class="sxs-lookup"><span data-stu-id="9168b-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="9168b-142">`Random` służy do wybierania kanału z listy za każdym razem, gdy wymagany jest kanał gRPC.</span><span class="sxs-lookup"><span data-stu-id="9168b-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="9168b-143">Używanie `Random` losowo dystrybuowanych wywołań przez wiele połączeń.</span><span class="sxs-lookup"><span data-stu-id="9168b-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9168b-144">Zwiększenie maksymalnego limitu współbieżności strumienia na serwerze jest innym sposobem rozwiązania tego problemu.</span><span class="sxs-lookup"><span data-stu-id="9168b-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="9168b-145">W Kestrel jest to konfiguracja przy użyciu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="9168b-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="9168b-146">Zwiększenie maksymalnego limitu współbieżności strumienia nie jest zalecane.</span><span class="sxs-lookup"><span data-stu-id="9168b-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="9168b-147">Zbyt wiele strumieni na pojedynczym połączeniu HTTP/2 wprowadza nowe problemy z wydajnością:</span><span class="sxs-lookup"><span data-stu-id="9168b-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="9168b-148">Rywalizacja o wątki między strumieniami próbującymi wykonać zapis w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="9168b-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="9168b-149">Utrata pakietów połączenia powoduje, że wszystkie wywołania są blokowane w warstwie TCP.</span><span class="sxs-lookup"><span data-stu-id="9168b-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="9168b-150">Równoważenie obciążenia</span><span class="sxs-lookup"><span data-stu-id="9168b-150">Load balancing</span></span>

<span data-ttu-id="9168b-151">Niektóre usługi równoważenia obciążenia nie działają efektywnie z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="9168b-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="9168b-152">Usługi równoważenia obciążenia P4 (transport) działają na poziomie połączenia przez dystrybuowanie połączeń TCP między punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="9168b-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="9168b-153">Takie podejście dobrze sprawdza się w przypadku ładowania wywołań interfejsu API równoważenia przy użyciu protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9168b-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="9168b-154">Współbieżne wywołania wykonane przy użyciu protokołu HTTP/1.1 są wysyłane w różnych połączeniach, co umożliwia Równoważenie obciążenia między punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="9168b-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="9168b-155">Moduły równoważenia obciążenia P4 działają na poziomie połączenia, ale nie działają prawidłowo z gRPC.</span><span class="sxs-lookup"><span data-stu-id="9168b-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="9168b-156">gRPC używa protokołu HTTP/2, który multipleksuje wiele wywołań w jednym połączeniu TCP.</span><span class="sxs-lookup"><span data-stu-id="9168b-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="9168b-157">Wszystkie wywołania gRPC za pośrednictwem tego połączenia przejdą do jednego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9168b-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="9168b-158">Dostępne są dwie opcje efektywnego równoważenia obciążenia gRPC:</span><span class="sxs-lookup"><span data-stu-id="9168b-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="9168b-159">Równoważenie obciążenia po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="9168b-159">Client-side load balancing</span></span>
* <span data-ttu-id="9168b-160">Równoważenie obciążenia serwera proxy P7 (aplikacji)</span><span class="sxs-lookup"><span data-stu-id="9168b-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="9168b-161">Tylko wywołania gRPC można równoważyć obciążeniem między punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="9168b-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="9168b-162">Po nawiązaniu połączenia gRPC przesyłania strumieniowego wszystkie komunikaty wysyłane przez strumień są przenoszone do jednego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9168b-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="9168b-163">Równoważenie obciążenia po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="9168b-163">Client-side load balancing</span></span>

<span data-ttu-id="9168b-164">Za pomocą równoważenia obciążenia po stronie klienta Klient zna informacje o punktach końcowych.</span><span class="sxs-lookup"><span data-stu-id="9168b-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="9168b-165">Dla każdego wywołania gRPC wybiera inny punkt końcowy do wysłania wywołania.</span><span class="sxs-lookup"><span data-stu-id="9168b-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="9168b-166">Równoważenie obciążenia po stronie klienta jest dobrym rozwiązaniem, gdy opóźnienie jest ważne.</span><span class="sxs-lookup"><span data-stu-id="9168b-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="9168b-167">Między klientem a usługą nie istnieje serwer proxy, dlatego wywołanie jest wysyłane bezpośrednio do usługi.</span><span class="sxs-lookup"><span data-stu-id="9168b-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="9168b-168">Minusem do równoważenia obciążenia po stronie klienta polega na tym, że każdy klient musi śledzić dostępne punkty końcowe, których należy użyć.</span><span class="sxs-lookup"><span data-stu-id="9168b-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="9168b-169">Referencyjna równoważenia obciążenia klienta jest techniką, w której stan równoważenia obciążenia jest przechowywany w centralnej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="9168b-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="9168b-170">Klienci okresowo wysyłają zapytanie do centralnej lokalizacji, aby uzyskać informacje do użycia podczas podejmowania decyzji dotyczących równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="9168b-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="9168b-171">`Grpc.Net.Client` obecnie nie obsługuje równoważenia obciążenia po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="9168b-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="9168b-172">[GRPC. Core](https://www.nuget.org/packages/Grpc.Core) to dobry wybór w przypadku, gdy w programie .NET jest wymagane Równoważenie obciążenia po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="9168b-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="9168b-173">Równoważenie obciążenia serwera proxy</span><span class="sxs-lookup"><span data-stu-id="9168b-173">Proxy load balancing</span></span>

<span data-ttu-id="9168b-174">Serwer proxy P7 (aplikacja) działa na wyższym poziomie niż serwer proxy P4 (transport).</span><span class="sxs-lookup"><span data-stu-id="9168b-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="9168b-175">Serwery proxy P7 mają świadomość protokołu HTTP/2 i umożliwiają dystrybucję wywołań gRPC do serwera proxy na jednym połączeniu HTTP/2 w wielu punktach końcowych.</span><span class="sxs-lookup"><span data-stu-id="9168b-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="9168b-176">Użycie serwera proxy jest prostsze niż Równoważenie obciążenia po stronie klienta, ale może zwiększyć dodatkowe opóźnienie dla wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="9168b-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="9168b-177">Istnieje wiele dostępnych serwerów proxy P7.</span><span class="sxs-lookup"><span data-stu-id="9168b-177">There are many L7 proxies available.</span></span> <span data-ttu-id="9168b-178">Dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="9168b-178">Some options are:</span></span>

* <span data-ttu-id="9168b-179">[Wysłannika](https://www.envoyproxy.io/) — popularny serwer proxy typu open source.</span><span class="sxs-lookup"><span data-stu-id="9168b-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="9168b-180">[Łącząca](https://linkerd.io/) się z siatką usługi dla Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="9168b-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="9168b-181">[YARP: odwrotny serwer](https://microsoft.github.io/reverse-proxy/) proxy — Podgląd typu open source zapisany w środowisku .NET.</span><span class="sxs-lookup"><span data-stu-id="9168b-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a><span data-ttu-id="9168b-182">Komunikacja między procesami</span><span class="sxs-lookup"><span data-stu-id="9168b-182">Inter-process communication</span></span>

<span data-ttu-id="9168b-183">wywołania gRPC między klientem a usługą są zwykle wysyłane za pośrednictwem gniazd TCP.</span><span class="sxs-lookup"><span data-stu-id="9168b-183">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="9168b-184">Protokół TCP doskonale nadaje się do komunikacji w sieci, ale [komunikacja między procesami (IPC)](https://wikipedia.org/wiki/Inter-process_communication) jest bardziej wydajna, gdy klient i usługa znajdują się na tym samym komputerze.</span><span class="sxs-lookup"><span data-stu-id="9168b-184">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span>

<span data-ttu-id="9168b-185">Rozważ użycie transportu, takiego jak gniazda domeny systemu UNIX lub nazwane potoki, dla wywołań gRPC między procesami na tym samym komputerze.</span><span class="sxs-lookup"><span data-stu-id="9168b-185">Consider using a transport like Unix domain sockets or named pipes for gRPC calls between processes on the same machine.</span></span> <span data-ttu-id="9168b-186">Aby uzyskać więcej informacji, zobacz <xref:grpc/interprocess>.</span><span class="sxs-lookup"><span data-stu-id="9168b-186">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="keep-alive-pings"></a><span data-ttu-id="9168b-187">Utrzymywanie aktywności poleceń ping</span><span class="sxs-lookup"><span data-stu-id="9168b-187">Keep alive pings</span></span>

<span data-ttu-id="9168b-188">Polecenia ping utrzymywania aktywności mogą służyć do utrzymywania połączeń HTTP/2 w okresach braku aktywności.</span><span class="sxs-lookup"><span data-stu-id="9168b-188">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="9168b-189">Istniejące połączenie HTTP/2 jest gotowe, gdy aplikacja wznawia działanie, umożliwia szybkie wywoływanie gRPC, bez opóźnień spowodowanych przez ponowne nawiązanie połączenia.</span><span class="sxs-lookup"><span data-stu-id="9168b-189">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="9168b-190">Monitoruj polecenia ping dla <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="9168b-190">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="9168b-191">Poprzedni kod konfiguruje kanał, który wysyła polecenie ping Keep Alive do serwera co 60 sekund w okresach braku aktywności.</span><span class="sxs-lookup"><span data-stu-id="9168b-191">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="9168b-192">Polecenie ping gwarantuje, że serwer i wszystkie używane serwery proxy nie będą zamykać połączenia z powodu braku aktywności.</span><span class="sxs-lookup"><span data-stu-id="9168b-192">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="9168b-193">Przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="9168b-193">Streaming</span></span>

<span data-ttu-id="9168b-194">gRPC dwukierunkowe przesyłanie strumieniowe może służyć do zastępowania jednoargumentowych wywołań gRPC w scenariuszach o wysokiej wydajności.</span><span class="sxs-lookup"><span data-stu-id="9168b-194">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="9168b-195">Po rozpoczęciu strumienia dwukierunkowego komunikaty przesyłające strumieniowo i z powrotem są szybsze niż wysyłanie komunikatów z wieloma jednoargumentowymi wywołaniami gRPC.</span><span class="sxs-lookup"><span data-stu-id="9168b-195">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="9168b-196">Komunikaty przesyłane strumieniowo są wysyłane jako dane na istniejącym żądaniu HTTP/2 i eliminuje obciążenie związane z tworzeniem nowego żądania HTTP/2 dla każdego wywołania jednoargumentowego.</span><span class="sxs-lookup"><span data-stu-id="9168b-196">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="9168b-197">Przykładowa usługa:</span><span class="sxs-lookup"><span data-stu-id="9168b-197">Example service:</span></span>

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

<span data-ttu-id="9168b-198">Przykładowy klient:</span><span class="sxs-lookup"><span data-stu-id="9168b-198">Example client:</span></span>

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

<span data-ttu-id="9168b-199">Zamiana wywołań jednoargumentowych z dwukierunkowym przesyłaniem strumieniowym ze względu na wydajność jest zaawansowaną techniką i nie jest odpowiednia w wielu sytuacjach</span><span class="sxs-lookup"><span data-stu-id="9168b-199">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="9168b-200">Używanie wywołań przesyłania strumieniowego jest dobrym rozwiązaniem w przypadku:</span><span class="sxs-lookup"><span data-stu-id="9168b-200">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="9168b-201">Wymagana jest Wysoka przepływność lub małe opóźnienia.</span><span class="sxs-lookup"><span data-stu-id="9168b-201">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="9168b-202">gRPC i HTTP/2 są identyfikowane jako wąskie gardła wydajności.</span><span class="sxs-lookup"><span data-stu-id="9168b-202">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="9168b-203">Proces roboczy w kliencie wysyła lub otrzymuje zwykłe wiadomości za pomocą usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="9168b-203">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="9168b-204">Należy pamiętać o dodatkowej złożoności i ograniczeniach użycia wywołań przesyłania strumieniowego zamiast jednoargumentowego:</span><span class="sxs-lookup"><span data-stu-id="9168b-204">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="9168b-205">Strumień może zostać przerwany przez usługę lub błąd połączenia.</span><span class="sxs-lookup"><span data-stu-id="9168b-205">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="9168b-206">Jeśli wystąpi błąd, logika jest wymagana do ponownego uruchomienia strumienia.</span><span class="sxs-lookup"><span data-stu-id="9168b-206">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="9168b-207">`RequestStream.WriteAsync` nie jest bezpieczna w przypadku wielowątkowości.</span><span class="sxs-lookup"><span data-stu-id="9168b-207">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="9168b-208">W danym momencie może być zapisany tylko jeden komunikat w strumieniu.</span><span class="sxs-lookup"><span data-stu-id="9168b-208">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="9168b-209">Wysyłanie komunikatów z wielu wątków za pośrednictwem pojedynczego strumienia wymaga kolejki producent/odbiorca, która jest taka sama jak <xref:System.Threading.Channels.Channel%601> w przypadku komunikatów.</span><span class="sxs-lookup"><span data-stu-id="9168b-209">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="9168b-210">Metoda przesyłania strumieniowego gRPC jest ograniczona do odebrania jednego typu komunikatu i wysłania jednego typu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="9168b-210">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="9168b-211">Na przykład `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` odbiera `RequestMessage` i wysyła `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="9168b-211">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="9168b-212">Protobuf obsługę komunikatów nieznanych lub warunkowych przy użyciu `Any` i może obejść `oneof` to ograniczenie.</span><span class="sxs-lookup"><span data-stu-id="9168b-212">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
