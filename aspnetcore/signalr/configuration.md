---
title: konfiguracja ASP.NET SignalR Core
author: bradygaster
description: Dowiedz się, jak skonfigurować SignalR aplikacje ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223988"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="a352e-103">Konfiguracja biblioteki SignalR platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a352e-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a352e-104">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a352e-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a352e-105">ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a352e-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a352e-106">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a352e-107">Serializacji JSON można skonfigurować na serwerze przy użyciu [addjsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="a352e-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a352e-108">`AddJsonProtocol`można dodać po [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a352e-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a352e-109">Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt.</span><span class="sxs-lookup"><span data-stu-id="a352e-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a352e-110">[Właściwość PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) na tym `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiekcie jest obiektem, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="a352e-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a352e-111">Aby uzyskać więcej informacji, zobacz [dokumentację System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a352e-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a352e-112">Na przykład, aby skonfigurować serializator, aby nie zmieniać wielkości liter nazw właściwości, zamiast domyślnych nazw "camelCase", użyj następującego kodu w: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="a352e-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="a352e-113">W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a352e-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a352e-114">Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a352e-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a352e-115">Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a352e-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a352e-116">Przełącz się na Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="a352e-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a352e-117">Jeśli potrzebujesz `Newtonsoft.Json` funkcji, które nie są `System.Text.Json`obsługiwane w , Zobacz [Switch do Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a352e-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a352e-118">Opcje serializacji pakietu MessagePack</span><span class="sxs-lookup"><span data-stu-id="a352e-118">MessagePack serialization options</span></span>

<span data-ttu-id="a352e-119">Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="a352e-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a352e-120">Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a352e-121">Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a352e-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a352e-122">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="a352e-122">Configure server options</span></span>

<span data-ttu-id="a352e-123">W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:</span><span class="sxs-lookup"><span data-stu-id="a352e-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a352e-124">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-124">Option</span></span> | <span data-ttu-id="a352e-125">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-125">Default Value</span></span> | <span data-ttu-id="a352e-126">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a352e-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-127">30 seconds</span></span> | <span data-ttu-id="a352e-128">Serwer uzna klienta rozłączony, jeśli nie otrzymał komunikatu (w tym keep-alive) w tym przedziale.</span><span class="sxs-lookup"><span data-stu-id="a352e-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a352e-129">Może upłynąć dłużej niż ten przedział limitu czasu dla klienta, aby faktycznie być oznaczone rozłączony, ze względu na sposób zaimplementowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a352e-130">Zalecana wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="a352e-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a352e-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-131">15 seconds</span></span> | <span data-ttu-id="a352e-132">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="a352e-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a352e-133">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="a352e-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a352e-134">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="a352e-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a352e-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-135">15 seconds</span></span> | <span data-ttu-id="a352e-136">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte.</span><span class="sxs-lookup"><span data-stu-id="a352e-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a352e-137">Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="a352e-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a352e-138">Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="a352e-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a352e-139">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="a352e-139">All installed protocols</span></span> | <span data-ttu-id="a352e-140">Protokoły obsługiwane przez ten koncentrator.</span><span class="sxs-lookup"><span data-stu-id="a352e-140">Protocols supported by this hub.</span></span> <span data-ttu-id="a352e-141">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów.</span><span class="sxs-lookup"><span data-stu-id="a352e-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a352e-142">Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody.</span><span class="sxs-lookup"><span data-stu-id="a352e-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a352e-143">Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje.</span><span class="sxs-lookup"><span data-stu-id="a352e-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a352e-144">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klienta.</span><span class="sxs-lookup"><span data-stu-id="a352e-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a352e-145">Jeśli ten limit zostanie osiągnięty, przetwarzanie wywołań jest blokowane, dopóki serwer przetwarza elementy strumienia.</span><span class="sxs-lookup"><span data-stu-id="a352e-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a352e-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="a352e-146">32 KB</span></span> | <span data-ttu-id="a352e-147">Maksymalny rozmiar pojedynczej przychodzącej wiadomości koncentratora.</span><span class="sxs-lookup"><span data-stu-id="a352e-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a352e-148">Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .</span><span class="sxs-lookup"><span data-stu-id="a352e-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a352e-149">Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:</span><span class="sxs-lookup"><span data-stu-id="a352e-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a352e-150">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="a352e-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a352e-151">Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="a352e-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a352e-152">Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w .</span><span class="sxs-lookup"><span data-stu-id="a352e-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="a352e-153">W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="a352e-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a352e-154">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-154">Option</span></span> | <span data-ttu-id="a352e-155">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-155">Default Value</span></span> | <span data-ttu-id="a352e-156">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a352e-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="a352e-157">32 KB</span></span> | <span data-ttu-id="a352e-158">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer przed zastosowaniem backpressure.</span><span class="sxs-lookup"><span data-stu-id="a352e-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a352e-159">Zwiększenie tej wartości umożliwia serwerowi szybciej odbierać większe wiadomości bez stosowania kompresji wsteczjej, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="a352e-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a352e-160">Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub.</span><span class="sxs-lookup"><span data-stu-id="a352e-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a352e-161">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="a352e-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a352e-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="a352e-162">32 KB</span></span> | <span data-ttu-id="a352e-163">Maksymalna liczba bajtów wysłanych przez aplikację, która buforuje serwer przed obserwowaniem wstecznego naciśnięcia.</span><span class="sxs-lookup"><span data-stu-id="a352e-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a352e-164">Zwiększenie tej wartości umożliwia serwerowi buforowanie większych wiadomości szybciej bez oczekiwania na ciśnienie wsteczne, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="a352e-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a352e-165">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="a352e-165">All Transports are enabled.</span></span> | <span data-ttu-id="a352e-166">Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia.</span><span class="sxs-lookup"><span data-stu-id="a352e-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a352e-167">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="a352e-167">See below.</span></span> | <span data-ttu-id="a352e-168">Dodatkowe opcje specyficzne dla transportu długie sondowanie.</span><span class="sxs-lookup"><span data-stu-id="a352e-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a352e-169">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="a352e-169">See below.</span></span> | <span data-ttu-id="a352e-170">Dodatkowe opcje specyficzne dla transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a352e-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a352e-171">Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="a352e-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a352e-172">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-172">Option</span></span> | <span data-ttu-id="a352e-173">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-173">Default Value</span></span> | <span data-ttu-id="a352e-174">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a352e-175">90 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-175">90 seconds</span></span> | <span data-ttu-id="a352e-176">Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a352e-177">Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej.</span><span class="sxs-lookup"><span data-stu-id="a352e-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a352e-178">Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="a352e-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a352e-179">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-179">Option</span></span> | <span data-ttu-id="a352e-180">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-180">Default Value</span></span> | <span data-ttu-id="a352e-181">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a352e-182">5 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-182">5 seconds</span></span> | <span data-ttu-id="a352e-183">Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="a352e-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a352e-184">Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="a352e-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a352e-185">Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość.</span><span class="sxs-lookup"><span data-stu-id="a352e-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a352e-186">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="a352e-186">Configure client options</span></span>

<span data-ttu-id="a352e-187">Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów).</span><span class="sxs-lookup"><span data-stu-id="a352e-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a352e-188">Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.</span><span class="sxs-lookup"><span data-stu-id="a352e-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a352e-189">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a352e-189">Configure logging</span></span>

<span data-ttu-id="a352e-190">Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="a352e-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a352e-191">Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze.</span><span class="sxs-lookup"><span data-stu-id="a352e-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a352e-192">Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="a352e-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a352e-193">Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety.</span><span class="sxs-lookup"><span data-stu-id="a352e-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a352e-194">Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.</span><span class="sxs-lookup"><span data-stu-id="a352e-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a352e-195">Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="a352e-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a352e-196">Wywołanie `AddConsole` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a352e-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a352e-197">W kliencie JavaScript `configureLogging` istnieje podobna metoda.</span><span class="sxs-lookup"><span data-stu-id="a352e-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a352e-198">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a352e-199">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a352e-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a352e-200">Zamiast `LogLevel` wartości można również podać wartość `string` reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="a352e-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a352e-201">Jest to przydatne podczas konfigurowania rejestrowania SignalR w środowiskach, `LogLevel` w których nie masz dostępu do stałych.</span><span class="sxs-lookup"><span data-stu-id="a352e-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a352e-202">W poniższej tabeli wymieniono dostępne poziomy dziennika.</span><span class="sxs-lookup"><span data-stu-id="a352e-202">The following table lists the available log levels.</span></span> <span data-ttu-id="a352e-203">Wartość poda `configureLogging` się do ustawia **minimalny** poziom dziennika, który będzie rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="a352e-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a352e-204">Wiadomości rejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="a352e-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a352e-205">Ciąg</span><span class="sxs-lookup"><span data-stu-id="a352e-205">String</span></span>                      | <span data-ttu-id="a352e-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a352e-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a352e-207">`info`**lub**`information`</span><span class="sxs-lookup"><span data-stu-id="a352e-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a352e-208">`warn`**lub**`warning`</span><span class="sxs-lookup"><span data-stu-id="a352e-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a352e-209">Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="a352e-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a352e-210">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a352e-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a352e-211">Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a352e-212">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a352e-213">Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a352e-214">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="a352e-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a352e-215">Można to bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="a352e-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a352e-216">Konfigurowanie dozwolonych transportów</span><span class="sxs-lookup"><span data-stu-id="a352e-216">Configure allowed transports</span></span>

<span data-ttu-id="a352e-217">Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a352e-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a352e-218">Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="a352e-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a352e-219">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="a352e-219">All transports are enabled by default.</span></span>

<span data-ttu-id="a352e-220">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:</span><span class="sxs-lookup"><span data-stu-id="a352e-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a352e-221">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:</span><span class="sxs-lookup"><span data-stu-id="a352e-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a352e-222">W tej wersji websockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="a352e-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a352e-223">W kliencie Java transport jest `withTransport` wybierany `HttpHubConnectionBuilder`za pomocą metody na pliku .</span><span class="sxs-lookup"><span data-stu-id="a352e-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a352e-224">Domyślnie klient Java używa transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a352e-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a352e-225">Klient Java SignalR nie obsługuje jeszcze awaryjnego transportu.</span><span class="sxs-lookup"><span data-stu-id="a352e-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a352e-226">Konfigurowanie uwierzytelniania na okaziciela</span><span class="sxs-lookup"><span data-stu-id="a352e-226">Configure bearer authentication</span></span>

<span data-ttu-id="a352e-227">Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="a352e-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a352e-228">W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ).</span><span class="sxs-lookup"><span data-stu-id="a352e-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a352e-229">W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets).</span><span class="sxs-lookup"><span data-stu-id="a352e-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a352e-230">W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .</span><span class="sxs-lookup"><span data-stu-id="a352e-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a352e-231">W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :</span><span class="sxs-lookup"><span data-stu-id="a352e-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a352e-232">W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :</span><span class="sxs-lookup"><span data-stu-id="a352e-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a352e-233">W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a352e-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a352e-234">Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a352e-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a352e-235">Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="a352e-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a352e-236">Konfigurowanie opcji limitu czasu i utrzymania przy życiu</span><span class="sxs-lookup"><span data-stu-id="a352e-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a352e-237">Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="a352e-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a352e-238">.NET</span><span class="sxs-lookup"><span data-stu-id="a352e-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a352e-239">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-239">Option</span></span> | <span data-ttu-id="a352e-240">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-240">Default value</span></span> | <span data-ttu-id="a352e-241">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a352e-242">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a352e-243">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-243">Timeout for server activity.</span></span> <span data-ttu-id="a352e-244">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a352e-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a352e-245">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="a352e-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a352e-246">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="a352e-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a352e-247">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-247">15 seconds</span></span> | <span data-ttu-id="a352e-248">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="a352e-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="a352e-249">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a352e-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a352e-250">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="a352e-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a352e-251">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="a352e-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a352e-252">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-252">15 seconds</span></span> | <span data-ttu-id="a352e-253">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="a352e-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a352e-254">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="a352e-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a352e-255">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="a352e-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a352e-256">W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.</span><span class="sxs-lookup"><span data-stu-id="a352e-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a352e-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a352e-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a352e-258">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-258">Option</span></span> | <span data-ttu-id="a352e-259">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-259">Default value</span></span> | <span data-ttu-id="a352e-260">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a352e-261">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a352e-262">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-262">Timeout for server activity.</span></span> <span data-ttu-id="a352e-263">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="a352e-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a352e-264">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="a352e-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a352e-265">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="a352e-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a352e-266">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a352e-267">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="a352e-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a352e-268">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="a352e-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a352e-269">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="a352e-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a352e-270">Java</span><span class="sxs-lookup"><span data-stu-id="a352e-270">Java</span></span>](#tab/java)

| <span data-ttu-id="a352e-271">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-271">Option</span></span> | <span data-ttu-id="a352e-272">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-272">Default value</span></span> | <span data-ttu-id="a352e-273">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a352e-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a352e-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a352e-275">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a352e-276">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-276">Timeout for server activity.</span></span> <span data-ttu-id="a352e-277">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="a352e-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a352e-278">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="a352e-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a352e-279">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="a352e-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a352e-280">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-280">15 seconds</span></span> | <span data-ttu-id="a352e-281">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="a352e-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="a352e-282">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="a352e-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a352e-283">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="a352e-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a352e-284">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="a352e-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a352e-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a352e-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a352e-286">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a352e-287">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="a352e-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a352e-288">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="a352e-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a352e-289">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="a352e-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a352e-290">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="a352e-290">Configure additional options</span></span>

<span data-ttu-id="a352e-291">Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="a352e-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a352e-292">.NET</span><span class="sxs-lookup"><span data-stu-id="a352e-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a352e-293">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="a352e-293">.NET Option</span></span> |  <span data-ttu-id="a352e-294">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-294">Default value</span></span> | <span data-ttu-id="a352e-295">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a352e-296">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a352e-297">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a352e-298">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="a352e-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a352e-299">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a352e-300">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-300">Empty</span></span> | <span data-ttu-id="a352e-301">Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="a352e-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a352e-302">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-302">Empty</span></span> | <span data-ttu-id="a352e-303">Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a352e-304">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-304">Empty</span></span> | <span data-ttu-id="a352e-305">Poświadczenia do wysłania przy użyciu każdego żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a352e-306">5 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-306">5 seconds</span></span> | <span data-ttu-id="a352e-307">WebSockets tylko.</span><span class="sxs-lookup"><span data-stu-id="a352e-307">WebSockets only.</span></span> <span data-ttu-id="a352e-308">Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="a352e-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a352e-309">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się.</span><span class="sxs-lookup"><span data-stu-id="a352e-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a352e-310">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-310">Empty</span></span> | <span data-ttu-id="a352e-311">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a352e-312">Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a352e-313">Nie jest używany dla połączeń WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a352e-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="a352e-314">Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="a352e-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a352e-315">Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="a352e-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a352e-316">**Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="a352e-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a352e-317">Serwer proxy HTTP używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a352e-318">Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a352e-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a352e-319">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="a352e-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a352e-320">Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a352e-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a352e-321">Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="a352e-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a352e-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a352e-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a352e-323">Opcja JavaScript</span><span class="sxs-lookup"><span data-stu-id="a352e-323">JavaScript Option</span></span> | <span data-ttu-id="a352e-324">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-324">Default Value</span></span> | <span data-ttu-id="a352e-325">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a352e-326">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a352e-327">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a352e-328">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="a352e-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a352e-329">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a352e-330">Java</span><span class="sxs-lookup"><span data-stu-id="a352e-330">Java</span></span>](#tab/java)

| <span data-ttu-id="a352e-331">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="a352e-331">Java Option</span></span> | <span data-ttu-id="a352e-332">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-332">Default Value</span></span> | <span data-ttu-id="a352e-333">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a352e-334">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a352e-335">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a352e-336">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="a352e-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a352e-337">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a352e-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a352e-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a352e-339">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-339">Empty</span></span> | <span data-ttu-id="a352e-340">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a352e-341">W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:</span><span class="sxs-lookup"><span data-stu-id="a352e-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a352e-342">W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:</span><span class="sxs-lookup"><span data-stu-id="a352e-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a352e-343">W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a352e-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a352e-344">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a352e-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a352e-345">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a352e-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a352e-346">ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a352e-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a352e-347">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a352e-348">Serializacji JSON można skonfigurować na serwerze przy użyciu [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia, które `Startup.ConfigureServices` mogą być dodawane po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w metodzie.</span><span class="sxs-lookup"><span data-stu-id="a352e-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a352e-349">Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt.</span><span class="sxs-lookup"><span data-stu-id="a352e-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a352e-350">[Właściwość PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) na tym obiekcie `JsonSerializerSettings` jest obiektem JSON.NET, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="a352e-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a352e-351">Aby uzyskać więcej informacji, zobacz [JSON.NET dokumentacji](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a352e-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a352e-352">Na przykład, aby skonfigurować serializator do używania nazw właściwości "PascalCase", zamiast domyślnych nazw "camelCase", użyj następującego kodu w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a352e-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a352e-353">W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a352e-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a352e-354">Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a352e-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a352e-355">Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a352e-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a352e-356">Opcje serializacji pakietu MessagePack</span><span class="sxs-lookup"><span data-stu-id="a352e-356">MessagePack serialization options</span></span>

<span data-ttu-id="a352e-357">Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="a352e-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a352e-358">Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a352e-359">Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a352e-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a352e-360">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="a352e-360">Configure server options</span></span>

<span data-ttu-id="a352e-361">W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:</span><span class="sxs-lookup"><span data-stu-id="a352e-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a352e-362">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-362">Option</span></span> | <span data-ttu-id="a352e-363">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-363">Default Value</span></span> | <span data-ttu-id="a352e-364">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a352e-365">30 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-365">30 seconds</span></span> | <span data-ttu-id="a352e-366">Serwer uzna klienta rozłączony, jeśli nie otrzymał komunikatu (w tym keep-alive) w tym przedziale.</span><span class="sxs-lookup"><span data-stu-id="a352e-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a352e-367">Może upłynąć dłużej niż ten przedział limitu czasu dla klienta, aby faktycznie być oznaczone rozłączony, ze względu na sposób zaimplementowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a352e-368">Zalecana wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="a352e-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a352e-369">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-369">15 seconds</span></span> | <span data-ttu-id="a352e-370">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="a352e-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a352e-371">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="a352e-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a352e-372">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="a352e-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a352e-373">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-373">15 seconds</span></span> | <span data-ttu-id="a352e-374">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte.</span><span class="sxs-lookup"><span data-stu-id="a352e-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a352e-375">Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="a352e-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a352e-376">Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="a352e-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a352e-377">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="a352e-377">All installed protocols</span></span> | <span data-ttu-id="a352e-378">Protokoły obsługiwane przez ten koncentrator.</span><span class="sxs-lookup"><span data-stu-id="a352e-378">Protocols supported by this hub.</span></span> <span data-ttu-id="a352e-379">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów.</span><span class="sxs-lookup"><span data-stu-id="a352e-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a352e-380">Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody.</span><span class="sxs-lookup"><span data-stu-id="a352e-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a352e-381">Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje.</span><span class="sxs-lookup"><span data-stu-id="a352e-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a352e-382">Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .</span><span class="sxs-lookup"><span data-stu-id="a352e-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a352e-383">Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:</span><span class="sxs-lookup"><span data-stu-id="a352e-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a352e-384">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="a352e-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a352e-385">Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="a352e-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a352e-386">Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w .</span><span class="sxs-lookup"><span data-stu-id="a352e-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="a352e-387">W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="a352e-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a352e-388">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-388">Option</span></span> | <span data-ttu-id="a352e-389">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-389">Default Value</span></span> | <span data-ttu-id="a352e-390">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a352e-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="a352e-391">32 KB</span></span> | <span data-ttu-id="a352e-392">Maksymalna liczba bajtów odebranych od klienta buforującego serwer.</span><span class="sxs-lookup"><span data-stu-id="a352e-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a352e-393">Zwiększenie tej wartości umożliwia serwerowi odbieranie większych komunikatów, ale może negatywnie wpłynąć na zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="a352e-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a352e-394">Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub.</span><span class="sxs-lookup"><span data-stu-id="a352e-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a352e-395">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="a352e-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a352e-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="a352e-396">32 KB</span></span> | <span data-ttu-id="a352e-397">Maksymalna liczba bajtów wysyłanych przez aplikację buforów serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a352e-398">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych wiadomości, ale może negatywnie wpłynąć na zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="a352e-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a352e-399">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="a352e-399">All Transports are enabled.</span></span> | <span data-ttu-id="a352e-400">Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia.</span><span class="sxs-lookup"><span data-stu-id="a352e-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a352e-401">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="a352e-401">See below.</span></span> | <span data-ttu-id="a352e-402">Dodatkowe opcje specyficzne dla transportu długie sondowanie.</span><span class="sxs-lookup"><span data-stu-id="a352e-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a352e-403">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="a352e-403">See below.</span></span> | <span data-ttu-id="a352e-404">Dodatkowe opcje specyficzne dla transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a352e-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a352e-405">Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="a352e-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a352e-406">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-406">Option</span></span> | <span data-ttu-id="a352e-407">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-407">Default Value</span></span> | <span data-ttu-id="a352e-408">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a352e-409">90 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-409">90 seconds</span></span> | <span data-ttu-id="a352e-410">Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a352e-411">Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej.</span><span class="sxs-lookup"><span data-stu-id="a352e-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a352e-412">Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="a352e-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a352e-413">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-413">Option</span></span> | <span data-ttu-id="a352e-414">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-414">Default Value</span></span> | <span data-ttu-id="a352e-415">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a352e-416">5 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-416">5 seconds</span></span> | <span data-ttu-id="a352e-417">Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="a352e-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a352e-418">Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="a352e-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a352e-419">Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość.</span><span class="sxs-lookup"><span data-stu-id="a352e-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a352e-420">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="a352e-420">Configure client options</span></span>

<span data-ttu-id="a352e-421">Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów).</span><span class="sxs-lookup"><span data-stu-id="a352e-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a352e-422">Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.</span><span class="sxs-lookup"><span data-stu-id="a352e-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a352e-423">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a352e-423">Configure logging</span></span>

<span data-ttu-id="a352e-424">Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="a352e-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a352e-425">Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze.</span><span class="sxs-lookup"><span data-stu-id="a352e-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a352e-426">Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="a352e-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a352e-427">Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety.</span><span class="sxs-lookup"><span data-stu-id="a352e-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a352e-428">Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.</span><span class="sxs-lookup"><span data-stu-id="a352e-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a352e-429">Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="a352e-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a352e-430">Wywołanie `AddConsole` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a352e-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a352e-431">W kliencie JavaScript `configureLogging` istnieje podobna metoda.</span><span class="sxs-lookup"><span data-stu-id="a352e-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a352e-432">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a352e-433">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a352e-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a352e-434">Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="a352e-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a352e-435">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a352e-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a352e-436">Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a352e-437">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a352e-438">Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a352e-439">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="a352e-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a352e-440">Można to bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="a352e-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a352e-441">Konfigurowanie dozwolonych transportów</span><span class="sxs-lookup"><span data-stu-id="a352e-441">Configure allowed transports</span></span>

<span data-ttu-id="a352e-442">Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a352e-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a352e-443">Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="a352e-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a352e-444">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="a352e-444">All transports are enabled by default.</span></span>

<span data-ttu-id="a352e-445">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:</span><span class="sxs-lookup"><span data-stu-id="a352e-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a352e-446">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:</span><span class="sxs-lookup"><span data-stu-id="a352e-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a352e-447">W tej wersji websockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="a352e-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a352e-448">Konfigurowanie uwierzytelniania na okaziciela</span><span class="sxs-lookup"><span data-stu-id="a352e-448">Configure bearer authentication</span></span>

<span data-ttu-id="a352e-449">Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="a352e-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a352e-450">W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ).</span><span class="sxs-lookup"><span data-stu-id="a352e-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a352e-451">W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets).</span><span class="sxs-lookup"><span data-stu-id="a352e-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a352e-452">W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .</span><span class="sxs-lookup"><span data-stu-id="a352e-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a352e-453">W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :</span><span class="sxs-lookup"><span data-stu-id="a352e-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a352e-454">W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :</span><span class="sxs-lookup"><span data-stu-id="a352e-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a352e-455">W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a352e-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a352e-456">Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a352e-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a352e-457">Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="a352e-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a352e-458">Konfigurowanie opcji limitu czasu i utrzymania przy życiu</span><span class="sxs-lookup"><span data-stu-id="a352e-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a352e-459">Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="a352e-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a352e-460">.NET</span><span class="sxs-lookup"><span data-stu-id="a352e-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a352e-461">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-461">Option</span></span> | <span data-ttu-id="a352e-462">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-462">Default value</span></span> | <span data-ttu-id="a352e-463">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a352e-464">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a352e-465">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-465">Timeout for server activity.</span></span> <span data-ttu-id="a352e-466">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a352e-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a352e-467">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="a352e-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a352e-468">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="a352e-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a352e-469">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-469">15 seconds</span></span> | <span data-ttu-id="a352e-470">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="a352e-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="a352e-471">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a352e-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a352e-472">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="a352e-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a352e-473">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="a352e-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a352e-474">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-474">15 seconds</span></span> | <span data-ttu-id="a352e-475">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="a352e-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a352e-476">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="a352e-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a352e-477">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="a352e-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a352e-478">W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.</span><span class="sxs-lookup"><span data-stu-id="a352e-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a352e-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a352e-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a352e-480">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-480">Option</span></span> | <span data-ttu-id="a352e-481">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-481">Default value</span></span> | <span data-ttu-id="a352e-482">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a352e-483">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a352e-484">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-484">Timeout for server activity.</span></span> <span data-ttu-id="a352e-485">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="a352e-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a352e-486">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="a352e-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a352e-487">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="a352e-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a352e-488">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a352e-489">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="a352e-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a352e-490">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="a352e-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a352e-491">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="a352e-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a352e-492">Java</span><span class="sxs-lookup"><span data-stu-id="a352e-492">Java</span></span>](#tab/java)

| <span data-ttu-id="a352e-493">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-493">Option</span></span> | <span data-ttu-id="a352e-494">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-494">Default value</span></span> | <span data-ttu-id="a352e-495">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a352e-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a352e-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a352e-497">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a352e-498">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-498">Timeout for server activity.</span></span> <span data-ttu-id="a352e-499">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="a352e-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a352e-500">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="a352e-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a352e-501">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="a352e-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a352e-502">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-502">15 seconds</span></span> | <span data-ttu-id="a352e-503">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="a352e-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="a352e-504">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="a352e-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a352e-505">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="a352e-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a352e-506">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="a352e-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a352e-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a352e-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a352e-508">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a352e-509">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="a352e-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a352e-510">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="a352e-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a352e-511">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="a352e-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a352e-512">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="a352e-512">Configure additional options</span></span>

<span data-ttu-id="a352e-513">Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="a352e-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a352e-514">.NET</span><span class="sxs-lookup"><span data-stu-id="a352e-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a352e-515">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="a352e-515">.NET Option</span></span> |  <span data-ttu-id="a352e-516">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-516">Default value</span></span> | <span data-ttu-id="a352e-517">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a352e-518">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a352e-519">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a352e-520">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="a352e-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a352e-521">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a352e-522">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-522">Empty</span></span> | <span data-ttu-id="a352e-523">Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="a352e-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a352e-524">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-524">Empty</span></span> | <span data-ttu-id="a352e-525">Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a352e-526">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-526">Empty</span></span> | <span data-ttu-id="a352e-527">Poświadczenia do wysłania przy użyciu każdego żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a352e-528">5 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-528">5 seconds</span></span> | <span data-ttu-id="a352e-529">WebSockets tylko.</span><span class="sxs-lookup"><span data-stu-id="a352e-529">WebSockets only.</span></span> <span data-ttu-id="a352e-530">Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="a352e-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a352e-531">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się.</span><span class="sxs-lookup"><span data-stu-id="a352e-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a352e-532">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-532">Empty</span></span> | <span data-ttu-id="a352e-533">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a352e-534">Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a352e-535">Nie jest używany dla połączeń WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a352e-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="a352e-536">Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="a352e-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a352e-537">Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="a352e-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a352e-538">**Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="a352e-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a352e-539">Serwer proxy HTTP używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a352e-540">Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a352e-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a352e-541">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="a352e-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a352e-542">Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a352e-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a352e-543">Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="a352e-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a352e-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a352e-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a352e-545">Opcja JavaScript</span><span class="sxs-lookup"><span data-stu-id="a352e-545">JavaScript Option</span></span> | <span data-ttu-id="a352e-546">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-546">Default Value</span></span> | <span data-ttu-id="a352e-547">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a352e-548">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a352e-549">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a352e-550">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="a352e-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a352e-551">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a352e-552">Java</span><span class="sxs-lookup"><span data-stu-id="a352e-552">Java</span></span>](#tab/java)

| <span data-ttu-id="a352e-553">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="a352e-553">Java Option</span></span> | <span data-ttu-id="a352e-554">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-554">Default Value</span></span> | <span data-ttu-id="a352e-555">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a352e-556">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a352e-557">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a352e-558">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="a352e-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a352e-559">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a352e-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a352e-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a352e-561">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-561">Empty</span></span> | <span data-ttu-id="a352e-562">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a352e-563">W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:</span><span class="sxs-lookup"><span data-stu-id="a352e-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a352e-564">W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:</span><span class="sxs-lookup"><span data-stu-id="a352e-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a352e-565">W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a352e-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a352e-566">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a352e-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a352e-567">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a352e-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a352e-568">ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a352e-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a352e-569">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a352e-570">Serializacji JSON można skonfigurować na serwerze przy użyciu [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia, które `Startup.ConfigureServices` mogą być dodawane po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w metodzie.</span><span class="sxs-lookup"><span data-stu-id="a352e-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a352e-571">Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt.</span><span class="sxs-lookup"><span data-stu-id="a352e-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a352e-572">[Właściwość PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) na tym obiekcie `JsonSerializerSettings` jest obiektem JSON.NET, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="a352e-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a352e-573">Aby uzyskać więcej informacji, zobacz [JSON.NET dokumentacji](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a352e-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a352e-574">Na przykład, aby skonfigurować serializator do używania nazw właściwości "PascalCase", zamiast domyślnych nazw "camelCase", użyj następującego kodu w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a352e-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a352e-575">W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a352e-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a352e-576">Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a352e-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a352e-577">Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a352e-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a352e-578">Opcje serializacji pakietu MessagePack</span><span class="sxs-lookup"><span data-stu-id="a352e-578">MessagePack serialization options</span></span>

<span data-ttu-id="a352e-579">Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="a352e-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a352e-580">Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a352e-581">Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a352e-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a352e-582">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="a352e-582">Configure server options</span></span>

<span data-ttu-id="a352e-583">W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:</span><span class="sxs-lookup"><span data-stu-id="a352e-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a352e-584">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-584">Option</span></span> | <span data-ttu-id="a352e-585">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-585">Default Value</span></span> | <span data-ttu-id="a352e-586">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="a352e-587">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-587">15 seconds</span></span> | <span data-ttu-id="a352e-588">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="a352e-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a352e-589">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="a352e-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a352e-590">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="a352e-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a352e-591">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-591">15 seconds</span></span> | <span data-ttu-id="a352e-592">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte.</span><span class="sxs-lookup"><span data-stu-id="a352e-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a352e-593">Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="a352e-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a352e-594">Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="a352e-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a352e-595">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="a352e-595">All installed protocols</span></span> | <span data-ttu-id="a352e-596">Protokoły obsługiwane przez ten koncentrator.</span><span class="sxs-lookup"><span data-stu-id="a352e-596">Protocols supported by this hub.</span></span> <span data-ttu-id="a352e-597">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów.</span><span class="sxs-lookup"><span data-stu-id="a352e-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a352e-598">Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody.</span><span class="sxs-lookup"><span data-stu-id="a352e-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a352e-599">Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje.</span><span class="sxs-lookup"><span data-stu-id="a352e-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a352e-600">Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .</span><span class="sxs-lookup"><span data-stu-id="a352e-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a352e-601">Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:</span><span class="sxs-lookup"><span data-stu-id="a352e-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a352e-602">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="a352e-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a352e-603">Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="a352e-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a352e-604">Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w .</span><span class="sxs-lookup"><span data-stu-id="a352e-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="a352e-605">W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="a352e-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a352e-606">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-606">Option</span></span> | <span data-ttu-id="a352e-607">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-607">Default Value</span></span> | <span data-ttu-id="a352e-608">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a352e-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="a352e-609">32 KB</span></span> | <span data-ttu-id="a352e-610">Maksymalna liczba bajtów odebranych od klienta buforującego serwer.</span><span class="sxs-lookup"><span data-stu-id="a352e-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a352e-611">Zwiększenie tej wartości umożliwia serwerowi odbieranie większych komunikatów, ale może negatywnie wpłynąć na zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="a352e-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a352e-612">Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub.</span><span class="sxs-lookup"><span data-stu-id="a352e-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a352e-613">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="a352e-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a352e-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="a352e-614">32 KB</span></span> | <span data-ttu-id="a352e-615">Maksymalna liczba bajtów wysyłanych przez aplikację buforów serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a352e-616">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych wiadomości, ale może negatywnie wpłynąć na zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="a352e-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a352e-617">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="a352e-617">All Transports are enabled.</span></span> | <span data-ttu-id="a352e-618">Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia.</span><span class="sxs-lookup"><span data-stu-id="a352e-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a352e-619">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="a352e-619">See below.</span></span> | <span data-ttu-id="a352e-620">Dodatkowe opcje specyficzne dla transportu długie sondowanie.</span><span class="sxs-lookup"><span data-stu-id="a352e-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a352e-621">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="a352e-621">See below.</span></span> | <span data-ttu-id="a352e-622">Dodatkowe opcje specyficzne dla transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a352e-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a352e-623">Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="a352e-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a352e-624">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-624">Option</span></span> | <span data-ttu-id="a352e-625">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-625">Default Value</span></span> | <span data-ttu-id="a352e-626">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a352e-627">90 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-627">90 seconds</span></span> | <span data-ttu-id="a352e-628">Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a352e-629">Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej.</span><span class="sxs-lookup"><span data-stu-id="a352e-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a352e-630">Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="a352e-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a352e-631">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-631">Option</span></span> | <span data-ttu-id="a352e-632">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-632">Default Value</span></span> | <span data-ttu-id="a352e-633">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a352e-634">5 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-634">5 seconds</span></span> | <span data-ttu-id="a352e-635">Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="a352e-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a352e-636">Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="a352e-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a352e-637">Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość.</span><span class="sxs-lookup"><span data-stu-id="a352e-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a352e-638">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="a352e-638">Configure client options</span></span>

<span data-ttu-id="a352e-639">Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów).</span><span class="sxs-lookup"><span data-stu-id="a352e-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a352e-640">Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.</span><span class="sxs-lookup"><span data-stu-id="a352e-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a352e-641">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a352e-641">Configure logging</span></span>

<span data-ttu-id="a352e-642">Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="a352e-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a352e-643">Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze.</span><span class="sxs-lookup"><span data-stu-id="a352e-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a352e-644">Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="a352e-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a352e-645">Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety.</span><span class="sxs-lookup"><span data-stu-id="a352e-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a352e-646">Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.</span><span class="sxs-lookup"><span data-stu-id="a352e-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a352e-647">Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="a352e-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a352e-648">Wywołanie `AddConsole` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a352e-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a352e-649">W kliencie JavaScript `configureLogging` istnieje podobna metoda.</span><span class="sxs-lookup"><span data-stu-id="a352e-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a352e-650">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a352e-651">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a352e-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a352e-652">Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="a352e-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a352e-653">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a352e-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a352e-654">Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a352e-655">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a352e-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a352e-656">Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a352e-657">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="a352e-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a352e-658">Można to bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="a352e-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a352e-659">Konfigurowanie dozwolonych transportów</span><span class="sxs-lookup"><span data-stu-id="a352e-659">Configure allowed transports</span></span>

<span data-ttu-id="a352e-660">Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a352e-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a352e-661">Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="a352e-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a352e-662">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="a352e-662">All transports are enabled by default.</span></span>

<span data-ttu-id="a352e-663">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:</span><span class="sxs-lookup"><span data-stu-id="a352e-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a352e-664">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:</span><span class="sxs-lookup"><span data-stu-id="a352e-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a352e-665">Konfigurowanie uwierzytelniania na okaziciela</span><span class="sxs-lookup"><span data-stu-id="a352e-665">Configure bearer authentication</span></span>

<span data-ttu-id="a352e-666">Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="a352e-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a352e-667">W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ).</span><span class="sxs-lookup"><span data-stu-id="a352e-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a352e-668">W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets).</span><span class="sxs-lookup"><span data-stu-id="a352e-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a352e-669">W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .</span><span class="sxs-lookup"><span data-stu-id="a352e-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a352e-670">W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :</span><span class="sxs-lookup"><span data-stu-id="a352e-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a352e-671">W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :</span><span class="sxs-lookup"><span data-stu-id="a352e-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a352e-672">W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a352e-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a352e-673">Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a352e-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a352e-674">Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="a352e-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a352e-675">Konfigurowanie opcji limitu czasu i utrzymania przy życiu</span><span class="sxs-lookup"><span data-stu-id="a352e-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a352e-676">Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="a352e-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a352e-677">.NET</span><span class="sxs-lookup"><span data-stu-id="a352e-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a352e-678">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-678">Option</span></span> | <span data-ttu-id="a352e-679">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-679">Default value</span></span> | <span data-ttu-id="a352e-680">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a352e-681">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a352e-682">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-682">Timeout for server activity.</span></span> <span data-ttu-id="a352e-683">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a352e-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a352e-684">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="a352e-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a352e-685">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="a352e-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a352e-686">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-686">15 seconds</span></span> | <span data-ttu-id="a352e-687">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="a352e-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="a352e-688">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a352e-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a352e-689">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="a352e-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a352e-690">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="a352e-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="a352e-691">W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.</span><span class="sxs-lookup"><span data-stu-id="a352e-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a352e-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a352e-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a352e-693">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-693">Option</span></span> | <span data-ttu-id="a352e-694">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-694">Default value</span></span> | <span data-ttu-id="a352e-695">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a352e-696">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a352e-697">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-697">Timeout for server activity.</span></span> <span data-ttu-id="a352e-698">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="a352e-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a352e-699">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="a352e-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a352e-700">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="a352e-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a352e-701">Java</span><span class="sxs-lookup"><span data-stu-id="a352e-701">Java</span></span>](#tab/java)

| <span data-ttu-id="a352e-702">Opcja</span><span class="sxs-lookup"><span data-stu-id="a352e-702">Option</span></span> | <span data-ttu-id="a352e-703">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-703">Default value</span></span> | <span data-ttu-id="a352e-704">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a352e-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a352e-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a352e-706">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="a352e-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a352e-707">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="a352e-707">Timeout for server activity.</span></span> <span data-ttu-id="a352e-708">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="a352e-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a352e-709">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="a352e-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a352e-710">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="a352e-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a352e-711">15 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-711">15 seconds</span></span> | <span data-ttu-id="a352e-712">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="a352e-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="a352e-713">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="a352e-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a352e-714">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="a352e-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a352e-715">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="a352e-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a352e-716">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="a352e-716">Configure additional options</span></span>

<span data-ttu-id="a352e-717">Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="a352e-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a352e-718">.NET</span><span class="sxs-lookup"><span data-stu-id="a352e-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a352e-719">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="a352e-719">.NET Option</span></span> |  <span data-ttu-id="a352e-720">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-720">Default value</span></span> | <span data-ttu-id="a352e-721">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a352e-722">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a352e-723">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a352e-724">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="a352e-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a352e-725">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a352e-726">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-726">Empty</span></span> | <span data-ttu-id="a352e-727">Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="a352e-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a352e-728">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-728">Empty</span></span> | <span data-ttu-id="a352e-729">Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a352e-730">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-730">Empty</span></span> | <span data-ttu-id="a352e-731">Poświadczenia do wysłania przy użyciu każdego żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a352e-732">5 sekund</span><span class="sxs-lookup"><span data-stu-id="a352e-732">5 seconds</span></span> | <span data-ttu-id="a352e-733">WebSockets tylko.</span><span class="sxs-lookup"><span data-stu-id="a352e-733">WebSockets only.</span></span> <span data-ttu-id="a352e-734">Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="a352e-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a352e-735">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się.</span><span class="sxs-lookup"><span data-stu-id="a352e-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a352e-736">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-736">Empty</span></span> | <span data-ttu-id="a352e-737">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a352e-738">Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a352e-739">Nie jest używany dla połączeń WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a352e-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="a352e-740">Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="a352e-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a352e-741">Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="a352e-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a352e-742">**Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="a352e-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a352e-743">Serwer proxy HTTP używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a352e-744">Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a352e-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a352e-745">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="a352e-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a352e-746">Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a352e-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a352e-747">Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="a352e-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a352e-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a352e-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a352e-749">Opcja JavaScript</span><span class="sxs-lookup"><span data-stu-id="a352e-749">JavaScript Option</span></span> | <span data-ttu-id="a352e-750">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-750">Default Value</span></span> | <span data-ttu-id="a352e-751">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a352e-752">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a352e-753">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a352e-754">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="a352e-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a352e-755">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a352e-756">Java</span><span class="sxs-lookup"><span data-stu-id="a352e-756">Java</span></span>](#tab/java)

| <span data-ttu-id="a352e-757">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="a352e-757">Java Option</span></span> | <span data-ttu-id="a352e-758">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="a352e-758">Default Value</span></span> | <span data-ttu-id="a352e-759">Opis</span><span class="sxs-lookup"><span data-stu-id="a352e-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a352e-760">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a352e-761">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="a352e-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a352e-762">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="a352e-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a352e-763">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a352e-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a352e-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a352e-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a352e-765">Pusty</span><span class="sxs-lookup"><span data-stu-id="a352e-765">Empty</span></span> | <span data-ttu-id="a352e-766">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a352e-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a352e-767">W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:</span><span class="sxs-lookup"><span data-stu-id="a352e-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a352e-768">W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:</span><span class="sxs-lookup"><span data-stu-id="a352e-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a352e-769">W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a352e-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a352e-770">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a352e-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
