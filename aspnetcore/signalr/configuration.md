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
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228143"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="ec53e-103">Konfiguracja biblioteki SignalR platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec53e-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ec53e-104">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ec53e-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ec53e-105">ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ec53e-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ec53e-106">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ec53e-107">Serializacji JSON można skonfigurować na serwerze przy użyciu [addjsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="ec53e-108">`AddJsonProtocol`można dodać po [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ec53e-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ec53e-109">Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt.</span><span class="sxs-lookup"><span data-stu-id="ec53e-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ec53e-110">[Właściwość PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) na tym `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiekcie jest obiektem, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="ec53e-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ec53e-111">Aby uzyskać więcej informacji, zobacz [dokumentację System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="ec53e-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="ec53e-112">Na przykład, aby skonfigurować serializator, aby nie zmieniać wielkości liter nazw właściwości, zamiast domyślnych nazw "camelCase", użyj następującego kodu w: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="ec53e-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="ec53e-113">W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ec53e-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ec53e-114">Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="ec53e-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ec53e-115">Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec53e-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="ec53e-116">Przełącz się na Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="ec53e-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="ec53e-117">Jeśli potrzebujesz `Newtonsoft.Json` funkcji, które nie są `System.Text.Json`obsługiwane w , Zobacz [Switch do Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="ec53e-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ec53e-118">Opcje serializacji pakietu MessagePack</span><span class="sxs-lookup"><span data-stu-id="ec53e-118">MessagePack serialization options</span></span>

<span data-ttu-id="ec53e-119">Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="ec53e-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ec53e-120">Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ec53e-121">Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec53e-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ec53e-122">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="ec53e-122">Configure server options</span></span>

<span data-ttu-id="ec53e-123">W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:</span><span class="sxs-lookup"><span data-stu-id="ec53e-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ec53e-124">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-124">Option</span></span> | <span data-ttu-id="ec53e-125">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-125">Default Value</span></span> | <span data-ttu-id="ec53e-126">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ec53e-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-127">30 seconds</span></span> | <span data-ttu-id="ec53e-128">Serwer uzna klienta rozłączony, jeśli nie otrzymał komunikatu (w tym keep-alive) w tym przedziale.</span><span class="sxs-lookup"><span data-stu-id="ec53e-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ec53e-129">Może upłynąć dłużej niż ten przedział limitu czasu dla klienta, aby faktycznie być oznaczone rozłączony, ze względu na sposób zaimplementowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ec53e-130">Zalecana wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ec53e-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-131">15 seconds</span></span> | <span data-ttu-id="ec53e-132">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="ec53e-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ec53e-133">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-134">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ec53e-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-135">15 seconds</span></span> | <span data-ttu-id="ec53e-136">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte.</span><span class="sxs-lookup"><span data-stu-id="ec53e-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ec53e-137">Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ec53e-138">Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ec53e-139">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="ec53e-139">All installed protocols</span></span> | <span data-ttu-id="ec53e-140">Protokoły obsługiwane przez ten koncentrator.</span><span class="sxs-lookup"><span data-stu-id="ec53e-140">Protocols supported by this hub.</span></span> <span data-ttu-id="ec53e-141">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ec53e-142">Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody.</span><span class="sxs-lookup"><span data-stu-id="ec53e-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ec53e-143">Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje.</span><span class="sxs-lookup"><span data-stu-id="ec53e-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="ec53e-144">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klienta.</span><span class="sxs-lookup"><span data-stu-id="ec53e-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="ec53e-145">Jeśli ten limit zostanie osiągnięty, przetwarzanie wywołań jest blokowane, dopóki serwer przetwarza elementy strumienia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="ec53e-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-146">32 KB</span></span> | <span data-ttu-id="ec53e-147">Maksymalny rozmiar pojedynczej przychodzącej wiadomości koncentratora.</span><span class="sxs-lookup"><span data-stu-id="ec53e-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="ec53e-148">Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .</span><span class="sxs-lookup"><span data-stu-id="ec53e-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ec53e-149">Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:</span><span class="sxs-lookup"><span data-stu-id="ec53e-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ec53e-150">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="ec53e-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ec53e-151">Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ec53e-152">Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w .</span><span class="sxs-lookup"><span data-stu-id="ec53e-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ec53e-153">W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="ec53e-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ec53e-154">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-154">Option</span></span> | <span data-ttu-id="ec53e-155">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-155">Default Value</span></span> | <span data-ttu-id="ec53e-156">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ec53e-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-157">32 KB</span></span> | <span data-ttu-id="ec53e-158">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer przed zastosowaniem backpressure.</span><span class="sxs-lookup"><span data-stu-id="ec53e-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="ec53e-159">Zwiększenie tej wartości umożliwia serwerowi szybciej odbierać większe wiadomości bez stosowania kompresji wsteczjej, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ec53e-160">Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub.</span><span class="sxs-lookup"><span data-stu-id="ec53e-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ec53e-161">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="ec53e-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ec53e-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-162">32 KB</span></span> | <span data-ttu-id="ec53e-163">Maksymalna liczba bajtów wysłanych przez aplikację, która buforuje serwer przed obserwowaniem wstecznego naciśnięcia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="ec53e-164">Zwiększenie tej wartości umożliwia serwerowi buforowanie większych wiadomości szybciej bez oczekiwania na ciśnienie wsteczne, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ec53e-165">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-165">All Transports are enabled.</span></span> | <span data-ttu-id="ec53e-166">Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ec53e-167">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-167">See below.</span></span> | <span data-ttu-id="ec53e-168">Dodatkowe opcje specyficzne dla transportu długie sondowanie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ec53e-169">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-169">See below.</span></span> | <span data-ttu-id="ec53e-170">Dodatkowe opcje specyficzne dla transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="ec53e-171">0</span><span class="sxs-lookup"><span data-stu-id="ec53e-171">0</span></span> | <span data-ttu-id="ec53e-172">Określ minimalną wersję protokołu negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="ec53e-173">Służy do ograniczania klientów do nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="ec53e-174">Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="ec53e-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ec53e-175">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-175">Option</span></span> | <span data-ttu-id="ec53e-176">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-176">Default Value</span></span> | <span data-ttu-id="ec53e-177">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ec53e-178">90 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-178">90 seconds</span></span> | <span data-ttu-id="ec53e-179">Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ec53e-180">Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ec53e-181">Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="ec53e-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ec53e-182">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-182">Option</span></span> | <span data-ttu-id="ec53e-183">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-183">Default Value</span></span> | <span data-ttu-id="ec53e-184">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ec53e-185">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-185">5 seconds</span></span> | <span data-ttu-id="ec53e-186">Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ec53e-187">Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="ec53e-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ec53e-188">Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ec53e-189">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="ec53e-189">Configure client options</span></span>

<span data-ttu-id="ec53e-190">Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów).</span><span class="sxs-lookup"><span data-stu-id="ec53e-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ec53e-191">Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.</span><span class="sxs-lookup"><span data-stu-id="ec53e-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ec53e-192">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="ec53e-192">Configure logging</span></span>

<span data-ttu-id="ec53e-193">Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="ec53e-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ec53e-194">Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ec53e-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ec53e-195">Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="ec53e-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ec53e-196">Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety.</span><span class="sxs-lookup"><span data-stu-id="ec53e-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ec53e-197">Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.</span><span class="sxs-lookup"><span data-stu-id="ec53e-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ec53e-198">Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="ec53e-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ec53e-199">Wywołanie `AddConsole` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="ec53e-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ec53e-200">W kliencie JavaScript `configureLogging` istnieje podobna metoda.</span><span class="sxs-lookup"><span data-stu-id="ec53e-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ec53e-201">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ec53e-202">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="ec53e-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="ec53e-203">Zamiast `LogLevel` wartości można również podać wartość `string` reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="ec53e-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="ec53e-204">Jest to przydatne podczas konfigurowania rejestrowania SignalR w środowiskach, `LogLevel` w których nie masz dostępu do stałych.</span><span class="sxs-lookup"><span data-stu-id="ec53e-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="ec53e-205">W poniższej tabeli wymieniono dostępne poziomy dziennika.</span><span class="sxs-lookup"><span data-stu-id="ec53e-205">The following table lists the available log levels.</span></span> <span data-ttu-id="ec53e-206">Wartość poda `configureLogging` się do ustawia **minimalny** poziom dziennika, który będzie rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="ec53e-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="ec53e-207">Wiadomości rejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="ec53e-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="ec53e-208">Ciąg</span><span class="sxs-lookup"><span data-stu-id="ec53e-208">String</span></span>                      | <span data-ttu-id="ec53e-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="ec53e-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="ec53e-210">`info`**lub**`information`</span><span class="sxs-lookup"><span data-stu-id="ec53e-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="ec53e-211">`warn`**lub**`warning`</span><span class="sxs-lookup"><span data-stu-id="ec53e-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="ec53e-212">Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="ec53e-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ec53e-213">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ec53e-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ec53e-214">Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ec53e-215">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ec53e-216">Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ec53e-217">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="ec53e-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ec53e-218">Można to bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="ec53e-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ec53e-219">Konfigurowanie dozwolonych transportów</span><span class="sxs-lookup"><span data-stu-id="ec53e-219">Configure allowed transports</span></span>

<span data-ttu-id="ec53e-220">Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ec53e-221">Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ec53e-222">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-222">All transports are enabled by default.</span></span>

<span data-ttu-id="ec53e-223">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:</span><span class="sxs-lookup"><span data-stu-id="ec53e-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ec53e-224">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:</span><span class="sxs-lookup"><span data-stu-id="ec53e-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="ec53e-225">W tej wersji websockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="ec53e-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="ec53e-226">W kliencie Java transport jest `withTransport` wybierany `HttpHubConnectionBuilder`za pomocą metody na pliku .</span><span class="sxs-lookup"><span data-stu-id="ec53e-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="ec53e-227">Domyślnie klient Java używa transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ec53e-228">Klient Java SignalR nie obsługuje jeszcze awaryjnego transportu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ec53e-229">Konfigurowanie uwierzytelniania na okaziciela</span><span class="sxs-lookup"><span data-stu-id="ec53e-229">Configure bearer authentication</span></span>

<span data-ttu-id="ec53e-230">Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ec53e-231">W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ).</span><span class="sxs-lookup"><span data-stu-id="ec53e-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ec53e-232">W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ec53e-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ec53e-233">W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .</span><span class="sxs-lookup"><span data-stu-id="ec53e-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ec53e-234">W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :</span><span class="sxs-lookup"><span data-stu-id="ec53e-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ec53e-235">W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :</span><span class="sxs-lookup"><span data-stu-id="ec53e-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="ec53e-236">W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ec53e-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ec53e-237">Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ec53e-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ec53e-238">Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="ec53e-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ec53e-239">Konfigurowanie opcji limitu czasu i utrzymania przy życiu</span><span class="sxs-lookup"><span data-stu-id="ec53e-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ec53e-240">Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="ec53e-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ec53e-241">.NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ec53e-242">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-242">Option</span></span> | <span data-ttu-id="ec53e-243">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-243">Default value</span></span> | <span data-ttu-id="ec53e-244">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ec53e-245">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-246">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-246">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-247">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ec53e-248">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-249">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ec53e-250">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-250">15 seconds</span></span> | <span data-ttu-id="ec53e-251">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="ec53e-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="ec53e-252">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ec53e-253">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-254">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ec53e-255">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-255">15 seconds</span></span> | <span data-ttu-id="ec53e-256">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="ec53e-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ec53e-257">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="ec53e-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ec53e-258">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="ec53e-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="ec53e-259">W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.</span><span class="sxs-lookup"><span data-stu-id="ec53e-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ec53e-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ec53e-261">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-261">Option</span></span> | <span data-ttu-id="ec53e-262">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-262">Default value</span></span> | <span data-ttu-id="ec53e-263">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ec53e-264">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-265">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-265">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-266">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ec53e-267">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-268">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="ec53e-269">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-270">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="ec53e-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ec53e-271">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="ec53e-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ec53e-272">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="ec53e-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ec53e-273">Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-273">Java</span></span>](#tab/java)

| <span data-ttu-id="ec53e-274">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-274">Option</span></span> | <span data-ttu-id="ec53e-275">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-275">Default value</span></span> | <span data-ttu-id="ec53e-276">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ec53e-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ec53e-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ec53e-278">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-279">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-279">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-280">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ec53e-281">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-282">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ec53e-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-283">15 seconds</span></span> | <span data-ttu-id="ec53e-284">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="ec53e-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="ec53e-285">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ec53e-286">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-287">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="ec53e-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="ec53e-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="ec53e-289">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-290">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="ec53e-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ec53e-291">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="ec53e-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ec53e-292">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="ec53e-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ec53e-293">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="ec53e-293">Configure additional options</span></span>

<span data-ttu-id="ec53e-294">Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="ec53e-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ec53e-295">.NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ec53e-296">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-296">.NET Option</span></span> |  <span data-ttu-id="ec53e-297">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-297">Default value</span></span> | <span data-ttu-id="ec53e-298">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ec53e-299">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ec53e-300">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-301">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-302">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ec53e-303">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-303">Empty</span></span> | <span data-ttu-id="ec53e-304">Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="ec53e-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ec53e-305">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-305">Empty</span></span> | <span data-ttu-id="ec53e-306">Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ec53e-307">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-307">Empty</span></span> | <span data-ttu-id="ec53e-308">Poświadczenia do wysłania przy użyciu każdego żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ec53e-309">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-309">5 seconds</span></span> | <span data-ttu-id="ec53e-310">WebSockets tylko.</span><span class="sxs-lookup"><span data-stu-id="ec53e-310">WebSockets only.</span></span> <span data-ttu-id="ec53e-311">Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ec53e-312">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się.</span><span class="sxs-lookup"><span data-stu-id="ec53e-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ec53e-313">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-313">Empty</span></span> | <span data-ttu-id="ec53e-314">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ec53e-315">Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ec53e-316">Nie jest używany dla połączeń WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ec53e-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="ec53e-317">Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="ec53e-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ec53e-318">Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ec53e-319">**Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ec53e-320">Serwer proxy HTTP używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ec53e-321">Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ec53e-322">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ec53e-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ec53e-323">Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ec53e-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ec53e-324">Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ec53e-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ec53e-326">Opcja JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-326">JavaScript Option</span></span> | <span data-ttu-id="ec53e-327">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-327">Default Value</span></span> | <span data-ttu-id="ec53e-328">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ec53e-329">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ec53e-330">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-331">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-332">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ec53e-333">Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-333">Java</span></span>](#tab/java)

| <span data-ttu-id="ec53e-334">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-334">Java Option</span></span> | <span data-ttu-id="ec53e-335">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-335">Default Value</span></span> | <span data-ttu-id="ec53e-336">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ec53e-337">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ec53e-338">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-339">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-340">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ec53e-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ec53e-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ec53e-342">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-342">Empty</span></span> | <span data-ttu-id="ec53e-343">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ec53e-344">W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:</span><span class="sxs-lookup"><span data-stu-id="ec53e-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ec53e-345">W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:</span><span class="sxs-lookup"><span data-stu-id="ec53e-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ec53e-346">W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ec53e-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ec53e-347">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ec53e-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ec53e-348">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ec53e-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ec53e-349">ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ec53e-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ec53e-350">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ec53e-351">Serializacji JSON można skonfigurować na serwerze przy użyciu [addjsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="ec53e-352">`AddJsonProtocol`można dodać po [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ec53e-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ec53e-353">Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt.</span><span class="sxs-lookup"><span data-stu-id="ec53e-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ec53e-354">[Właściwość PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) na tym `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiekcie jest obiektem, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="ec53e-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ec53e-355">Aby uzyskać więcej informacji, zobacz [dokumentację System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="ec53e-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="ec53e-356">Na przykład, aby skonfigurować serializator, aby nie zmieniać wielkości liter nazw właściwości, zamiast domyślnych nazw "camelCase", użyj następującego kodu w: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="ec53e-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="ec53e-357">W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ec53e-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ec53e-358">Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="ec53e-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ec53e-359">Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec53e-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="ec53e-360">Przełącz się na Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="ec53e-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="ec53e-361">Jeśli potrzebujesz `Newtonsoft.Json` funkcji, które nie są `System.Text.Json`obsługiwane w , Zobacz [Switch do Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="ec53e-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ec53e-362">Opcje serializacji pakietu MessagePack</span><span class="sxs-lookup"><span data-stu-id="ec53e-362">MessagePack serialization options</span></span>

<span data-ttu-id="ec53e-363">Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="ec53e-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ec53e-364">Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ec53e-365">Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec53e-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ec53e-366">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="ec53e-366">Configure server options</span></span>

<span data-ttu-id="ec53e-367">W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:</span><span class="sxs-lookup"><span data-stu-id="ec53e-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ec53e-368">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-368">Option</span></span> | <span data-ttu-id="ec53e-369">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-369">Default Value</span></span> | <span data-ttu-id="ec53e-370">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ec53e-371">30 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-371">30 seconds</span></span> | <span data-ttu-id="ec53e-372">Serwer uzna klienta rozłączony, jeśli nie otrzymał komunikatu (w tym keep-alive) w tym przedziale.</span><span class="sxs-lookup"><span data-stu-id="ec53e-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ec53e-373">Może upłynąć dłużej niż ten przedział limitu czasu dla klienta, aby faktycznie być oznaczone rozłączony, ze względu na sposób zaimplementowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ec53e-374">Zalecana wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ec53e-375">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-375">15 seconds</span></span> | <span data-ttu-id="ec53e-376">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="ec53e-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ec53e-377">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-378">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ec53e-379">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-379">15 seconds</span></span> | <span data-ttu-id="ec53e-380">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte.</span><span class="sxs-lookup"><span data-stu-id="ec53e-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ec53e-381">Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ec53e-382">Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ec53e-383">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="ec53e-383">All installed protocols</span></span> | <span data-ttu-id="ec53e-384">Protokoły obsługiwane przez ten koncentrator.</span><span class="sxs-lookup"><span data-stu-id="ec53e-384">Protocols supported by this hub.</span></span> <span data-ttu-id="ec53e-385">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ec53e-386">Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody.</span><span class="sxs-lookup"><span data-stu-id="ec53e-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ec53e-387">Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje.</span><span class="sxs-lookup"><span data-stu-id="ec53e-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="ec53e-388">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klienta.</span><span class="sxs-lookup"><span data-stu-id="ec53e-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="ec53e-389">Jeśli ten limit zostanie osiągnięty, przetwarzanie wywołań jest blokowane, dopóki serwer przetwarza elementy strumienia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="ec53e-390">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-390">32 KB</span></span> | <span data-ttu-id="ec53e-391">Maksymalny rozmiar pojedynczej przychodzącej wiadomości koncentratora.</span><span class="sxs-lookup"><span data-stu-id="ec53e-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="ec53e-392">Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .</span><span class="sxs-lookup"><span data-stu-id="ec53e-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ec53e-393">Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:</span><span class="sxs-lookup"><span data-stu-id="ec53e-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ec53e-394">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="ec53e-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ec53e-395">Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ec53e-396">Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w .</span><span class="sxs-lookup"><span data-stu-id="ec53e-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ec53e-397">W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="ec53e-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ec53e-398">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-398">Option</span></span> | <span data-ttu-id="ec53e-399">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-399">Default Value</span></span> | <span data-ttu-id="ec53e-400">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ec53e-401">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-401">32 KB</span></span> | <span data-ttu-id="ec53e-402">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer przed zastosowaniem backpressure.</span><span class="sxs-lookup"><span data-stu-id="ec53e-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="ec53e-403">Zwiększenie tej wartości umożliwia serwerowi szybciej odbierać większe wiadomości bez stosowania kompresji wsteczjej, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ec53e-404">Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub.</span><span class="sxs-lookup"><span data-stu-id="ec53e-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ec53e-405">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="ec53e-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ec53e-406">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-406">32 KB</span></span> | <span data-ttu-id="ec53e-407">Maksymalna liczba bajtów wysłanych przez aplikację, która buforuje serwer przed obserwowaniem wstecznego naciśnięcia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="ec53e-408">Zwiększenie tej wartości umożliwia serwerowi buforowanie większych wiadomości szybciej bez oczekiwania na ciśnienie wsteczne, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ec53e-409">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-409">All Transports are enabled.</span></span> | <span data-ttu-id="ec53e-410">Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ec53e-411">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-411">See below.</span></span> | <span data-ttu-id="ec53e-412">Dodatkowe opcje specyficzne dla transportu długie sondowanie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ec53e-413">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-413">See below.</span></span> | <span data-ttu-id="ec53e-414">Dodatkowe opcje specyficzne dla transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ec53e-415">Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="ec53e-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ec53e-416">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-416">Option</span></span> | <span data-ttu-id="ec53e-417">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-417">Default Value</span></span> | <span data-ttu-id="ec53e-418">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ec53e-419">90 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-419">90 seconds</span></span> | <span data-ttu-id="ec53e-420">Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ec53e-421">Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ec53e-422">Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="ec53e-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ec53e-423">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-423">Option</span></span> | <span data-ttu-id="ec53e-424">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-424">Default Value</span></span> | <span data-ttu-id="ec53e-425">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ec53e-426">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-426">5 seconds</span></span> | <span data-ttu-id="ec53e-427">Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ec53e-428">Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="ec53e-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ec53e-429">Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ec53e-430">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="ec53e-430">Configure client options</span></span>

<span data-ttu-id="ec53e-431">Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów).</span><span class="sxs-lookup"><span data-stu-id="ec53e-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ec53e-432">Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.</span><span class="sxs-lookup"><span data-stu-id="ec53e-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ec53e-433">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="ec53e-433">Configure logging</span></span>

<span data-ttu-id="ec53e-434">Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="ec53e-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ec53e-435">Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ec53e-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ec53e-436">Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="ec53e-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ec53e-437">Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety.</span><span class="sxs-lookup"><span data-stu-id="ec53e-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ec53e-438">Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.</span><span class="sxs-lookup"><span data-stu-id="ec53e-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ec53e-439">Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="ec53e-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ec53e-440">Wywołanie `AddConsole` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="ec53e-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ec53e-441">W kliencie JavaScript `configureLogging` istnieje podobna metoda.</span><span class="sxs-lookup"><span data-stu-id="ec53e-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ec53e-442">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ec53e-443">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="ec53e-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="ec53e-444">Zamiast `LogLevel` wartości można również podać wartość `string` reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="ec53e-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="ec53e-445">Jest to przydatne podczas konfigurowania rejestrowania SignalR w środowiskach, `LogLevel` w których nie masz dostępu do stałych.</span><span class="sxs-lookup"><span data-stu-id="ec53e-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="ec53e-446">W poniższej tabeli wymieniono dostępne poziomy dziennika.</span><span class="sxs-lookup"><span data-stu-id="ec53e-446">The following table lists the available log levels.</span></span> <span data-ttu-id="ec53e-447">Wartość poda `configureLogging` się do ustawia **minimalny** poziom dziennika, który będzie rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="ec53e-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="ec53e-448">Wiadomości rejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="ec53e-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="ec53e-449">Ciąg</span><span class="sxs-lookup"><span data-stu-id="ec53e-449">String</span></span>                      | <span data-ttu-id="ec53e-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="ec53e-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="ec53e-451">`info`**lub**`information`</span><span class="sxs-lookup"><span data-stu-id="ec53e-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="ec53e-452">`warn`**lub**`warning`</span><span class="sxs-lookup"><span data-stu-id="ec53e-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="ec53e-453">Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="ec53e-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ec53e-454">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ec53e-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ec53e-455">Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ec53e-456">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ec53e-457">Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ec53e-458">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="ec53e-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ec53e-459">Można to bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="ec53e-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ec53e-460">Konfigurowanie dozwolonych transportów</span><span class="sxs-lookup"><span data-stu-id="ec53e-460">Configure allowed transports</span></span>

<span data-ttu-id="ec53e-461">Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ec53e-462">Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ec53e-463">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-463">All transports are enabled by default.</span></span>

<span data-ttu-id="ec53e-464">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:</span><span class="sxs-lookup"><span data-stu-id="ec53e-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ec53e-465">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:</span><span class="sxs-lookup"><span data-stu-id="ec53e-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="ec53e-466">W tej wersji websockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="ec53e-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="ec53e-467">W kliencie Java transport jest `withTransport` wybierany `HttpHubConnectionBuilder`za pomocą metody na pliku .</span><span class="sxs-lookup"><span data-stu-id="ec53e-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="ec53e-468">Domyślnie klient Java używa transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ec53e-469">Klient Java SignalR nie obsługuje jeszcze awaryjnego transportu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ec53e-470">Konfigurowanie uwierzytelniania na okaziciela</span><span class="sxs-lookup"><span data-stu-id="ec53e-470">Configure bearer authentication</span></span>

<span data-ttu-id="ec53e-471">Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ec53e-472">W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ).</span><span class="sxs-lookup"><span data-stu-id="ec53e-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ec53e-473">W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ec53e-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ec53e-474">W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .</span><span class="sxs-lookup"><span data-stu-id="ec53e-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ec53e-475">W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :</span><span class="sxs-lookup"><span data-stu-id="ec53e-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ec53e-476">W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :</span><span class="sxs-lookup"><span data-stu-id="ec53e-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="ec53e-477">W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ec53e-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ec53e-478">Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ec53e-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ec53e-479">Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="ec53e-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ec53e-480">Konfigurowanie opcji limitu czasu i utrzymania przy życiu</span><span class="sxs-lookup"><span data-stu-id="ec53e-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ec53e-481">Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="ec53e-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ec53e-482">.NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ec53e-483">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-483">Option</span></span> | <span data-ttu-id="ec53e-484">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-484">Default value</span></span> | <span data-ttu-id="ec53e-485">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ec53e-486">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-487">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-487">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-488">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ec53e-489">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-490">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ec53e-491">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-491">15 seconds</span></span> | <span data-ttu-id="ec53e-492">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="ec53e-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="ec53e-493">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ec53e-494">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-495">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ec53e-496">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-496">15 seconds</span></span> | <span data-ttu-id="ec53e-497">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="ec53e-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ec53e-498">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="ec53e-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ec53e-499">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="ec53e-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="ec53e-500">W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.</span><span class="sxs-lookup"><span data-stu-id="ec53e-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ec53e-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ec53e-502">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-502">Option</span></span> | <span data-ttu-id="ec53e-503">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-503">Default value</span></span> | <span data-ttu-id="ec53e-504">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ec53e-505">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-506">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-506">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-507">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ec53e-508">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-509">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="ec53e-510">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-511">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="ec53e-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ec53e-512">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="ec53e-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ec53e-513">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="ec53e-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ec53e-514">Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-514">Java</span></span>](#tab/java)

| <span data-ttu-id="ec53e-515">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-515">Option</span></span> | <span data-ttu-id="ec53e-516">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-516">Default value</span></span> | <span data-ttu-id="ec53e-517">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ec53e-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ec53e-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ec53e-519">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-520">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-520">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-521">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ec53e-522">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-523">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ec53e-524">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-524">15 seconds</span></span> | <span data-ttu-id="ec53e-525">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="ec53e-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="ec53e-526">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ec53e-527">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-528">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="ec53e-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="ec53e-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="ec53e-530">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-531">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="ec53e-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ec53e-532">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="ec53e-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ec53e-533">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="ec53e-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ec53e-534">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="ec53e-534">Configure additional options</span></span>

<span data-ttu-id="ec53e-535">Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="ec53e-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ec53e-536">.NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ec53e-537">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-537">.NET Option</span></span> |  <span data-ttu-id="ec53e-538">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-538">Default value</span></span> | <span data-ttu-id="ec53e-539">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ec53e-540">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ec53e-541">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-542">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-543">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ec53e-544">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-544">Empty</span></span> | <span data-ttu-id="ec53e-545">Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="ec53e-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ec53e-546">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-546">Empty</span></span> | <span data-ttu-id="ec53e-547">Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ec53e-548">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-548">Empty</span></span> | <span data-ttu-id="ec53e-549">Poświadczenia do wysłania przy użyciu każdego żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ec53e-550">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-550">5 seconds</span></span> | <span data-ttu-id="ec53e-551">WebSockets tylko.</span><span class="sxs-lookup"><span data-stu-id="ec53e-551">WebSockets only.</span></span> <span data-ttu-id="ec53e-552">Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ec53e-553">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się.</span><span class="sxs-lookup"><span data-stu-id="ec53e-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ec53e-554">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-554">Empty</span></span> | <span data-ttu-id="ec53e-555">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ec53e-556">Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ec53e-557">Nie jest używany dla połączeń WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ec53e-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="ec53e-558">Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="ec53e-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ec53e-559">Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ec53e-560">**Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ec53e-561">Serwer proxy HTTP używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ec53e-562">Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ec53e-563">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ec53e-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ec53e-564">Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ec53e-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ec53e-565">Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ec53e-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ec53e-567">Opcja JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-567">JavaScript Option</span></span> | <span data-ttu-id="ec53e-568">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-568">Default Value</span></span> | <span data-ttu-id="ec53e-569">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ec53e-570">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ec53e-571">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-572">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-573">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ec53e-574">Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-574">Java</span></span>](#tab/java)

| <span data-ttu-id="ec53e-575">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-575">Java Option</span></span> | <span data-ttu-id="ec53e-576">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-576">Default Value</span></span> | <span data-ttu-id="ec53e-577">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ec53e-578">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ec53e-579">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-580">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-581">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ec53e-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ec53e-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ec53e-583">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-583">Empty</span></span> | <span data-ttu-id="ec53e-584">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ec53e-585">W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:</span><span class="sxs-lookup"><span data-stu-id="ec53e-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ec53e-586">W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:</span><span class="sxs-lookup"><span data-stu-id="ec53e-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ec53e-587">W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ec53e-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ec53e-588">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ec53e-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ec53e-589">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ec53e-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ec53e-590">ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ec53e-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ec53e-591">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ec53e-592">Serializacji JSON można skonfigurować na serwerze przy użyciu [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia, które `Startup.ConfigureServices` mogą być dodawane po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w metodzie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ec53e-593">Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt.</span><span class="sxs-lookup"><span data-stu-id="ec53e-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ec53e-594">[Właściwość PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) na tym obiekcie `JsonSerializerSettings` jest obiektem JSON.NET, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="ec53e-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ec53e-595">Aby uzyskać więcej informacji, zobacz [JSON.NET dokumentacji](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="ec53e-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="ec53e-596">Na przykład, aby skonfigurować serializator do używania nazw właściwości "PascalCase", zamiast domyślnych nazw "camelCase", użyj następującego kodu w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ec53e-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="ec53e-597">W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ec53e-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ec53e-598">Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="ec53e-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ec53e-599">Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec53e-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ec53e-600">Opcje serializacji pakietu MessagePack</span><span class="sxs-lookup"><span data-stu-id="ec53e-600">MessagePack serialization options</span></span>

<span data-ttu-id="ec53e-601">Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="ec53e-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ec53e-602">Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ec53e-603">Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec53e-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ec53e-604">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="ec53e-604">Configure server options</span></span>

<span data-ttu-id="ec53e-605">W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:</span><span class="sxs-lookup"><span data-stu-id="ec53e-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ec53e-606">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-606">Option</span></span> | <span data-ttu-id="ec53e-607">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-607">Default Value</span></span> | <span data-ttu-id="ec53e-608">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ec53e-609">30 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-609">30 seconds</span></span> | <span data-ttu-id="ec53e-610">Serwer uzna klienta rozłączony, jeśli nie otrzymał komunikatu (w tym keep-alive) w tym przedziale.</span><span class="sxs-lookup"><span data-stu-id="ec53e-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ec53e-611">Może upłynąć dłużej niż ten przedział limitu czasu dla klienta, aby faktycznie być oznaczone rozłączony, ze względu na sposób zaimplementowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ec53e-612">Zalecana wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ec53e-613">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-613">15 seconds</span></span> | <span data-ttu-id="ec53e-614">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="ec53e-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ec53e-615">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-616">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ec53e-617">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-617">15 seconds</span></span> | <span data-ttu-id="ec53e-618">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte.</span><span class="sxs-lookup"><span data-stu-id="ec53e-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ec53e-619">Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ec53e-620">Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ec53e-621">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="ec53e-621">All installed protocols</span></span> | <span data-ttu-id="ec53e-622">Protokoły obsługiwane przez ten koncentrator.</span><span class="sxs-lookup"><span data-stu-id="ec53e-622">Protocols supported by this hub.</span></span> <span data-ttu-id="ec53e-623">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ec53e-624">Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody.</span><span class="sxs-lookup"><span data-stu-id="ec53e-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ec53e-625">Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje.</span><span class="sxs-lookup"><span data-stu-id="ec53e-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="ec53e-626">Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .</span><span class="sxs-lookup"><span data-stu-id="ec53e-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ec53e-627">Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:</span><span class="sxs-lookup"><span data-stu-id="ec53e-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ec53e-628">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="ec53e-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ec53e-629">Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ec53e-630">Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w .</span><span class="sxs-lookup"><span data-stu-id="ec53e-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ec53e-631">W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="ec53e-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ec53e-632">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-632">Option</span></span> | <span data-ttu-id="ec53e-633">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-633">Default Value</span></span> | <span data-ttu-id="ec53e-634">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ec53e-635">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-635">32 KB</span></span> | <span data-ttu-id="ec53e-636">Maksymalna liczba bajtów odebranych od klienta buforującego serwer.</span><span class="sxs-lookup"><span data-stu-id="ec53e-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="ec53e-637">Zwiększenie tej wartości umożliwia serwerowi odbieranie większych komunikatów, ale może negatywnie wpłynąć na zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ec53e-638">Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub.</span><span class="sxs-lookup"><span data-stu-id="ec53e-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ec53e-639">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="ec53e-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ec53e-640">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-640">32 KB</span></span> | <span data-ttu-id="ec53e-641">Maksymalna liczba bajtów wysyłanych przez aplikację buforów serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="ec53e-642">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych wiadomości, ale może negatywnie wpłynąć na zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ec53e-643">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-643">All Transports are enabled.</span></span> | <span data-ttu-id="ec53e-644">Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ec53e-645">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-645">See below.</span></span> | <span data-ttu-id="ec53e-646">Dodatkowe opcje specyficzne dla transportu długie sondowanie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ec53e-647">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-647">See below.</span></span> | <span data-ttu-id="ec53e-648">Dodatkowe opcje specyficzne dla transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ec53e-649">Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="ec53e-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ec53e-650">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-650">Option</span></span> | <span data-ttu-id="ec53e-651">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-651">Default Value</span></span> | <span data-ttu-id="ec53e-652">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ec53e-653">90 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-653">90 seconds</span></span> | <span data-ttu-id="ec53e-654">Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ec53e-655">Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ec53e-656">Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="ec53e-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ec53e-657">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-657">Option</span></span> | <span data-ttu-id="ec53e-658">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-658">Default Value</span></span> | <span data-ttu-id="ec53e-659">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ec53e-660">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-660">5 seconds</span></span> | <span data-ttu-id="ec53e-661">Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ec53e-662">Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="ec53e-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ec53e-663">Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ec53e-664">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="ec53e-664">Configure client options</span></span>

<span data-ttu-id="ec53e-665">Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów).</span><span class="sxs-lookup"><span data-stu-id="ec53e-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ec53e-666">Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.</span><span class="sxs-lookup"><span data-stu-id="ec53e-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ec53e-667">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="ec53e-667">Configure logging</span></span>

<span data-ttu-id="ec53e-668">Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="ec53e-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ec53e-669">Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ec53e-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ec53e-670">Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="ec53e-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ec53e-671">Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety.</span><span class="sxs-lookup"><span data-stu-id="ec53e-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ec53e-672">Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.</span><span class="sxs-lookup"><span data-stu-id="ec53e-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ec53e-673">Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="ec53e-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ec53e-674">Wywołanie `AddConsole` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="ec53e-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ec53e-675">W kliencie JavaScript `configureLogging` istnieje podobna metoda.</span><span class="sxs-lookup"><span data-stu-id="ec53e-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ec53e-676">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ec53e-677">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="ec53e-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ec53e-678">Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="ec53e-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ec53e-679">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ec53e-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ec53e-680">Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ec53e-681">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ec53e-682">Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ec53e-683">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="ec53e-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ec53e-684">Można to bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="ec53e-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ec53e-685">Konfigurowanie dozwolonych transportów</span><span class="sxs-lookup"><span data-stu-id="ec53e-685">Configure allowed transports</span></span>

<span data-ttu-id="ec53e-686">Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ec53e-687">Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ec53e-688">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-688">All transports are enabled by default.</span></span>

<span data-ttu-id="ec53e-689">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:</span><span class="sxs-lookup"><span data-stu-id="ec53e-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ec53e-690">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:</span><span class="sxs-lookup"><span data-stu-id="ec53e-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="ec53e-691">W tej wersji websockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="ec53e-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ec53e-692">Konfigurowanie uwierzytelniania na okaziciela</span><span class="sxs-lookup"><span data-stu-id="ec53e-692">Configure bearer authentication</span></span>

<span data-ttu-id="ec53e-693">Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ec53e-694">W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ).</span><span class="sxs-lookup"><span data-stu-id="ec53e-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ec53e-695">W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ec53e-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ec53e-696">W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .</span><span class="sxs-lookup"><span data-stu-id="ec53e-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ec53e-697">W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :</span><span class="sxs-lookup"><span data-stu-id="ec53e-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ec53e-698">W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :</span><span class="sxs-lookup"><span data-stu-id="ec53e-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="ec53e-699">W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ec53e-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ec53e-700">Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ec53e-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ec53e-701">Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="ec53e-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ec53e-702">Konfigurowanie opcji limitu czasu i utrzymania przy życiu</span><span class="sxs-lookup"><span data-stu-id="ec53e-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ec53e-703">Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="ec53e-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ec53e-704">.NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ec53e-705">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-705">Option</span></span> | <span data-ttu-id="ec53e-706">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-706">Default value</span></span> | <span data-ttu-id="ec53e-707">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ec53e-708">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-709">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-709">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-710">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ec53e-711">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-712">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ec53e-713">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-713">15 seconds</span></span> | <span data-ttu-id="ec53e-714">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="ec53e-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="ec53e-715">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ec53e-716">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-717">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ec53e-718">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-718">15 seconds</span></span> | <span data-ttu-id="ec53e-719">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="ec53e-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ec53e-720">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="ec53e-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ec53e-721">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="ec53e-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="ec53e-722">W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.</span><span class="sxs-lookup"><span data-stu-id="ec53e-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ec53e-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ec53e-724">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-724">Option</span></span> | <span data-ttu-id="ec53e-725">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-725">Default value</span></span> | <span data-ttu-id="ec53e-726">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ec53e-727">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-728">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-728">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-729">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ec53e-730">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-731">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="ec53e-732">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-733">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="ec53e-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ec53e-734">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="ec53e-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ec53e-735">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="ec53e-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ec53e-736">Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-736">Java</span></span>](#tab/java)

| <span data-ttu-id="ec53e-737">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-737">Option</span></span> | <span data-ttu-id="ec53e-738">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-738">Default value</span></span> | <span data-ttu-id="ec53e-739">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ec53e-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ec53e-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ec53e-741">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-742">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-742">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-743">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ec53e-744">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-745">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ec53e-746">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-746">15 seconds</span></span> | <span data-ttu-id="ec53e-747">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="ec53e-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="ec53e-748">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ec53e-749">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-750">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="ec53e-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="ec53e-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="ec53e-752">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-753">Określa interwał, w którym klient wysyła komunikaty ping.</span><span class="sxs-lookup"><span data-stu-id="ec53e-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ec53e-754">Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="ec53e-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ec53e-755">Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony.</span><span class="sxs-lookup"><span data-stu-id="ec53e-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ec53e-756">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="ec53e-756">Configure additional options</span></span>

<span data-ttu-id="ec53e-757">Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="ec53e-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ec53e-758">.NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ec53e-759">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-759">.NET Option</span></span> |  <span data-ttu-id="ec53e-760">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-760">Default value</span></span> | <span data-ttu-id="ec53e-761">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ec53e-762">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ec53e-763">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-764">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-765">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ec53e-766">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-766">Empty</span></span> | <span data-ttu-id="ec53e-767">Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="ec53e-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ec53e-768">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-768">Empty</span></span> | <span data-ttu-id="ec53e-769">Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ec53e-770">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-770">Empty</span></span> | <span data-ttu-id="ec53e-771">Poświadczenia do wysłania przy użyciu każdego żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ec53e-772">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-772">5 seconds</span></span> | <span data-ttu-id="ec53e-773">WebSockets tylko.</span><span class="sxs-lookup"><span data-stu-id="ec53e-773">WebSockets only.</span></span> <span data-ttu-id="ec53e-774">Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ec53e-775">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się.</span><span class="sxs-lookup"><span data-stu-id="ec53e-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ec53e-776">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-776">Empty</span></span> | <span data-ttu-id="ec53e-777">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ec53e-778">Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ec53e-779">Nie jest używany dla połączeń WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ec53e-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="ec53e-780">Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="ec53e-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ec53e-781">Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ec53e-782">**Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ec53e-783">Serwer proxy HTTP używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ec53e-784">Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ec53e-785">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ec53e-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ec53e-786">Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ec53e-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ec53e-787">Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ec53e-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ec53e-789">Opcja JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-789">JavaScript Option</span></span> | <span data-ttu-id="ec53e-790">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-790">Default Value</span></span> | <span data-ttu-id="ec53e-791">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ec53e-792">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ec53e-793">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-794">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-795">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ec53e-796">Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-796">Java</span></span>](#tab/java)

| <span data-ttu-id="ec53e-797">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-797">Java Option</span></span> | <span data-ttu-id="ec53e-798">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-798">Default Value</span></span> | <span data-ttu-id="ec53e-799">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ec53e-800">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ec53e-801">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-802">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-803">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ec53e-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ec53e-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ec53e-805">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-805">Empty</span></span> | <span data-ttu-id="ec53e-806">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ec53e-807">W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:</span><span class="sxs-lookup"><span data-stu-id="ec53e-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ec53e-808">W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:</span><span class="sxs-lookup"><span data-stu-id="ec53e-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ec53e-809">W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ec53e-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ec53e-810">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ec53e-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ec53e-811">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ec53e-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ec53e-812">ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ec53e-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ec53e-813">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ec53e-814">Serializacji JSON można skonfigurować na serwerze przy użyciu [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia, które `Startup.ConfigureServices` mogą być dodawane po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w metodzie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ec53e-815">Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt.</span><span class="sxs-lookup"><span data-stu-id="ec53e-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ec53e-816">[Właściwość PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) na tym obiekcie `JsonSerializerSettings` jest obiektem JSON.NET, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="ec53e-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ec53e-817">Aby uzyskać więcej informacji, zobacz [JSON.NET dokumentacji](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="ec53e-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="ec53e-818">Na przykład, aby skonfigurować serializator do używania nazw właściwości "PascalCase", zamiast domyślnych nazw "camelCase", użyj następującego kodu w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ec53e-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="ec53e-819">W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ec53e-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ec53e-820">Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="ec53e-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ec53e-821">Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec53e-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ec53e-822">Opcje serializacji pakietu MessagePack</span><span class="sxs-lookup"><span data-stu-id="ec53e-822">MessagePack serialization options</span></span>

<span data-ttu-id="ec53e-823">Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="ec53e-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ec53e-824">Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ec53e-825">Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec53e-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ec53e-826">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="ec53e-826">Configure server options</span></span>

<span data-ttu-id="ec53e-827">W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:</span><span class="sxs-lookup"><span data-stu-id="ec53e-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ec53e-828">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-828">Option</span></span> | <span data-ttu-id="ec53e-829">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-829">Default Value</span></span> | <span data-ttu-id="ec53e-830">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="ec53e-831">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-831">15 seconds</span></span> | <span data-ttu-id="ec53e-832">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="ec53e-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ec53e-833">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-834">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ec53e-835">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-835">15 seconds</span></span> | <span data-ttu-id="ec53e-836">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte.</span><span class="sxs-lookup"><span data-stu-id="ec53e-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ec53e-837">Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ec53e-838">Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ec53e-839">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="ec53e-839">All installed protocols</span></span> | <span data-ttu-id="ec53e-840">Protokoły obsługiwane przez ten koncentrator.</span><span class="sxs-lookup"><span data-stu-id="ec53e-840">Protocols supported by this hub.</span></span> <span data-ttu-id="ec53e-841">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ec53e-842">Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody.</span><span class="sxs-lookup"><span data-stu-id="ec53e-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ec53e-843">Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje.</span><span class="sxs-lookup"><span data-stu-id="ec53e-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="ec53e-844">Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .</span><span class="sxs-lookup"><span data-stu-id="ec53e-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ec53e-845">Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:</span><span class="sxs-lookup"><span data-stu-id="ec53e-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ec53e-846">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="ec53e-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ec53e-847">Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ec53e-848">Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w .</span><span class="sxs-lookup"><span data-stu-id="ec53e-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ec53e-849">W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="ec53e-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ec53e-850">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-850">Option</span></span> | <span data-ttu-id="ec53e-851">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-851">Default Value</span></span> | <span data-ttu-id="ec53e-852">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ec53e-853">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-853">32 KB</span></span> | <span data-ttu-id="ec53e-854">Maksymalna liczba bajtów odebranych od klienta buforującego serwer.</span><span class="sxs-lookup"><span data-stu-id="ec53e-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="ec53e-855">Zwiększenie tej wartości umożliwia serwerowi odbieranie większych komunikatów, ale może negatywnie wpłynąć na zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ec53e-856">Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub.</span><span class="sxs-lookup"><span data-stu-id="ec53e-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ec53e-857">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="ec53e-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ec53e-858">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec53e-858">32 KB</span></span> | <span data-ttu-id="ec53e-859">Maksymalna liczba bajtów wysyłanych przez aplikację buforów serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="ec53e-860">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych wiadomości, ale może negatywnie wpłynąć na zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ec53e-861">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-861">All Transports are enabled.</span></span> | <span data-ttu-id="ec53e-862">Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ec53e-863">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-863">See below.</span></span> | <span data-ttu-id="ec53e-864">Dodatkowe opcje specyficzne dla transportu długie sondowanie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ec53e-865">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-865">See below.</span></span> | <span data-ttu-id="ec53e-866">Dodatkowe opcje specyficzne dla transportu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ec53e-867">Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="ec53e-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ec53e-868">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-868">Option</span></span> | <span data-ttu-id="ec53e-869">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-869">Default Value</span></span> | <span data-ttu-id="ec53e-870">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ec53e-871">90 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-871">90 seconds</span></span> | <span data-ttu-id="ec53e-872">Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ec53e-873">Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej.</span><span class="sxs-lookup"><span data-stu-id="ec53e-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ec53e-874">Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:</span><span class="sxs-lookup"><span data-stu-id="ec53e-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ec53e-875">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-875">Option</span></span> | <span data-ttu-id="ec53e-876">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-876">Default Value</span></span> | <span data-ttu-id="ec53e-877">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ec53e-878">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-878">5 seconds</span></span> | <span data-ttu-id="ec53e-879">Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ec53e-880">Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="ec53e-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ec53e-881">Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość.</span><span class="sxs-lookup"><span data-stu-id="ec53e-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ec53e-882">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="ec53e-882">Configure client options</span></span>

<span data-ttu-id="ec53e-883">Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów).</span><span class="sxs-lookup"><span data-stu-id="ec53e-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ec53e-884">Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.</span><span class="sxs-lookup"><span data-stu-id="ec53e-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ec53e-885">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="ec53e-885">Configure logging</span></span>

<span data-ttu-id="ec53e-886">Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="ec53e-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ec53e-887">Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ec53e-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ec53e-888">Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="ec53e-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ec53e-889">Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety.</span><span class="sxs-lookup"><span data-stu-id="ec53e-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ec53e-890">Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.</span><span class="sxs-lookup"><span data-stu-id="ec53e-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ec53e-891">Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="ec53e-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ec53e-892">Wywołanie `AddConsole` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="ec53e-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ec53e-893">W kliencie JavaScript `configureLogging` istnieje podobna metoda.</span><span class="sxs-lookup"><span data-stu-id="ec53e-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ec53e-894">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ec53e-895">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="ec53e-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ec53e-896">Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="ec53e-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ec53e-897">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ec53e-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ec53e-898">Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ec53e-899">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ec53e-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ec53e-900">Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ec53e-901">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="ec53e-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ec53e-902">Można to bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="ec53e-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ec53e-903">Konfigurowanie dozwolonych transportów</span><span class="sxs-lookup"><span data-stu-id="ec53e-903">Configure allowed transports</span></span>

<span data-ttu-id="ec53e-904">Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ec53e-905">Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ec53e-906">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="ec53e-906">All transports are enabled by default.</span></span>

<span data-ttu-id="ec53e-907">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:</span><span class="sxs-lookup"><span data-stu-id="ec53e-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ec53e-908">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:</span><span class="sxs-lookup"><span data-stu-id="ec53e-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ec53e-909">Konfigurowanie uwierzytelniania na okaziciela</span><span class="sxs-lookup"><span data-stu-id="ec53e-909">Configure bearer authentication</span></span>

<span data-ttu-id="ec53e-910">Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ec53e-911">W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ).</span><span class="sxs-lookup"><span data-stu-id="ec53e-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ec53e-912">W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ec53e-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ec53e-913">W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .</span><span class="sxs-lookup"><span data-stu-id="ec53e-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ec53e-914">W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :</span><span class="sxs-lookup"><span data-stu-id="ec53e-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ec53e-915">W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :</span><span class="sxs-lookup"><span data-stu-id="ec53e-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="ec53e-916">W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ec53e-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ec53e-917">Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ec53e-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ec53e-918">Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="ec53e-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ec53e-919">Konfigurowanie opcji limitu czasu i utrzymania przy życiu</span><span class="sxs-lookup"><span data-stu-id="ec53e-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ec53e-920">Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="ec53e-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ec53e-921">.NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ec53e-922">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-922">Option</span></span> | <span data-ttu-id="ec53e-923">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-923">Default value</span></span> | <span data-ttu-id="ec53e-924">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ec53e-925">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-926">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-926">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-927">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ec53e-928">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-929">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ec53e-930">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-930">15 seconds</span></span> | <span data-ttu-id="ec53e-931">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="ec53e-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="ec53e-932">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ec53e-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ec53e-933">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-934">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="ec53e-935">W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.</span><span class="sxs-lookup"><span data-stu-id="ec53e-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ec53e-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ec53e-937">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-937">Option</span></span> | <span data-ttu-id="ec53e-938">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-938">Default value</span></span> | <span data-ttu-id="ec53e-939">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ec53e-940">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-941">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-941">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-942">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ec53e-943">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-944">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ec53e-945">Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-945">Java</span></span>](#tab/java)

| <span data-ttu-id="ec53e-946">Opcja</span><span class="sxs-lookup"><span data-stu-id="ec53e-946">Option</span></span> | <span data-ttu-id="ec53e-947">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-947">Default value</span></span> | <span data-ttu-id="ec53e-948">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ec53e-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ec53e-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ec53e-950">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ec53e-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ec53e-951">Limit czasu dla aktywności serwera.</span><span class="sxs-lookup"><span data-stu-id="ec53e-951">Timeout for server activity.</span></span> <span data-ttu-id="ec53e-952">Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ec53e-953">Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ec53e-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ec53e-954">Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów.</span><span class="sxs-lookup"><span data-stu-id="ec53e-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ec53e-955">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-955">15 seconds</span></span> | <span data-ttu-id="ec53e-956">Limit czasu dla uzgadniania serwera początkowego.</span><span class="sxs-lookup"><span data-stu-id="ec53e-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="ec53e-957">Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ec53e-958">Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="ec53e-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ec53e-959">Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)</span><span class="sxs-lookup"><span data-stu-id="ec53e-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ec53e-960">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="ec53e-960">Configure additional options</span></span>

<span data-ttu-id="ec53e-961">Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="ec53e-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ec53e-962">.NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ec53e-963">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="ec53e-963">.NET Option</span></span> |  <span data-ttu-id="ec53e-964">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-964">Default value</span></span> | <span data-ttu-id="ec53e-965">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ec53e-966">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ec53e-967">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-968">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-969">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ec53e-970">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-970">Empty</span></span> | <span data-ttu-id="ec53e-971">Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="ec53e-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ec53e-972">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-972">Empty</span></span> | <span data-ttu-id="ec53e-973">Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ec53e-974">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-974">Empty</span></span> | <span data-ttu-id="ec53e-975">Poświadczenia do wysłania przy użyciu każdego żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ec53e-976">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ec53e-976">5 seconds</span></span> | <span data-ttu-id="ec53e-977">WebSockets tylko.</span><span class="sxs-lookup"><span data-stu-id="ec53e-977">WebSockets only.</span></span> <span data-ttu-id="ec53e-978">Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="ec53e-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ec53e-979">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się.</span><span class="sxs-lookup"><span data-stu-id="ec53e-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ec53e-980">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-980">Empty</span></span> | <span data-ttu-id="ec53e-981">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ec53e-982">Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ec53e-983">Nie jest używany dla połączeń WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ec53e-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="ec53e-984">Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="ec53e-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ec53e-985">Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="ec53e-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ec53e-986">**Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ec53e-987">Serwer proxy HTTP używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ec53e-988">Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ec53e-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ec53e-989">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ec53e-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ec53e-990">Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ec53e-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ec53e-991">Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ec53e-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ec53e-993">Opcja JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec53e-993">JavaScript Option</span></span> | <span data-ttu-id="ec53e-994">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-994">Default Value</span></span> | <span data-ttu-id="ec53e-995">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ec53e-996">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ec53e-997">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-998">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-999">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ec53e-1000">Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="ec53e-1001">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="ec53e-1001">Java Option</span></span> | <span data-ttu-id="ec53e-1002">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="ec53e-1002">Default Value</span></span> | <span data-ttu-id="ec53e-1003">Opis</span><span class="sxs-lookup"><span data-stu-id="ec53e-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ec53e-1004">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ec53e-1005">Ustaw to, aby `true` pominąć krok negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ec53e-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ec53e-1006">**Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.**</span><span class="sxs-lookup"><span data-stu-id="ec53e-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ec53e-1007">Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ec53e-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ec53e-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ec53e-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ec53e-1009">Pusty</span><span class="sxs-lookup"><span data-stu-id="ec53e-1009">Empty</span></span> | <span data-ttu-id="ec53e-1010">Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec53e-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ec53e-1011">W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:</span><span class="sxs-lookup"><span data-stu-id="ec53e-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ec53e-1012">W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:</span><span class="sxs-lookup"><span data-stu-id="ec53e-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ec53e-1013">W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ec53e-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ec53e-1014">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ec53e-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
