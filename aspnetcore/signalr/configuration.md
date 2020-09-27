---
title: SignalRKonfiguracja ASP.NET Core
author: bradygaster
description: Dowiedz się, jak skonfigurować SignalR aplikacje ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393876"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="f33f3-103">SignalRKonfiguracja ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f33f3-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f33f3-104">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-105">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f33f3-106">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f33f3-107">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="f33f3-108">`AddJsonProtocol`można dodać po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do programu `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f33f3-109">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="f33f3-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f33f3-110">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f33f3-111">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="f33f3-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="f33f3-112">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="f33f3-113">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="f33f3-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f33f3-114">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f33f3-115">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="f33f3-116">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="f33f3-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="f33f3-117">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="f33f3-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f33f3-118">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-118">MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-119">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f33f3-120">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-121">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f33f3-122">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="f33f3-122">Configure server options</span></span>

<span data-ttu-id="f33f3-123">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="f33f3-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="f33f3-124">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-124">Option</span></span> | <span data-ttu-id="f33f3-125">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-125">Default Value</span></span> | <span data-ttu-id="f33f3-126">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f33f3-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-127">30 seconds</span></span> | <span data-ttu-id="f33f3-128">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="f33f3-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f33f3-129">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f33f3-130">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="f33f3-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f33f3-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-131">15 seconds</span></span> | <span data-ttu-id="f33f3-132">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="f33f3-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f33f3-133">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-134">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f33f3-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-135">15 seconds</span></span> | <span data-ttu-id="f33f3-136">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f33f3-137">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f33f3-138">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="f33f3-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f33f3-139">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="f33f3-139">All installed protocols</span></span> | <span data-ttu-id="f33f3-140">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-140">Protocols supported by this hub.</span></span> <span data-ttu-id="f33f3-141">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f33f3-142">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f33f3-143">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="f33f3-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="f33f3-144">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="f33f3-145">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="f33f3-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-146">32 KB</span></span> | <span data-ttu-id="f33f3-147">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="f33f3-148">1</span><span class="sxs-lookup"><span data-stu-id="f33f3-148">1</span></span> | <span data-ttu-id="f33f3-149">Maksymalna liczba metod centralnych, które każdy klient może wywołać równolegle przed kolejkowanie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="f33f3-150">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="f33f3-151">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f33f3-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f33f3-152">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="f33f3-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f33f3-153">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f33f3-154">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="f33f3-155">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f33f3-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="f33f3-156">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-156">Option</span></span> | <span data-ttu-id="f33f3-157">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-157">Default Value</span></span> | <span data-ttu-id="f33f3-158">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f33f3-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-159">32 KB</span></span> | <span data-ttu-id="f33f3-160">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="f33f3-161">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f33f3-162">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f33f3-163">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f33f3-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-164">32 KB</span></span> | <span data-ttu-id="f33f3-165">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="f33f3-166">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f33f3-167">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-167">All Transports are enabled.</span></span> | <span data-ttu-id="f33f3-168">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f33f3-169">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-169">See below.</span></span> | <span data-ttu-id="f33f3-170">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f33f3-171">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-171">See below.</span></span> | <span data-ttu-id="f33f3-172">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="f33f3-173">0</span><span class="sxs-lookup"><span data-stu-id="f33f3-173">0</span></span> | <span data-ttu-id="f33f3-174">Określ minimalną wersję protokołu negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="f33f3-175">Służy do ograniczania liczby klientów do nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="f33f3-176">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f33f3-177">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-177">Option</span></span> | <span data-ttu-id="f33f3-178">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-178">Default Value</span></span> | <span data-ttu-id="f33f3-179">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f33f3-180">90 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-180">90 seconds</span></span> | <span data-ttu-id="f33f3-181">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f33f3-182">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f33f3-183">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f33f3-184">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-184">Option</span></span> | <span data-ttu-id="f33f3-185">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-185">Default Value</span></span> | <span data-ttu-id="f33f3-186">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f33f3-187">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-187">5 seconds</span></span> | <span data-ttu-id="f33f3-188">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f33f3-189">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="f33f3-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f33f3-190">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f33f3-191">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="f33f3-191">Configure client options</span></span>

<span data-ttu-id="f33f3-192">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f33f3-193">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f33f3-194">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f33f3-194">Configure logging</span></span>

<span data-ttu-id="f33f3-195">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="f33f3-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f33f3-196">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="f33f3-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f33f3-197">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-198">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="f33f3-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f33f3-199">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="f33f3-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f33f3-200">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="f33f3-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f33f3-201">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f33f3-202">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="f33f3-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f33f3-203">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f33f3-204">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f33f3-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="f33f3-205">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="f33f3-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="f33f3-206">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="f33f3-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="f33f3-207">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="f33f3-207">The following table lists the available log levels.</span></span> <span data-ttu-id="f33f3-208">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="f33f3-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="f33f3-209">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="f33f3-210">String</span><span class="sxs-lookup"><span data-stu-id="f33f3-210">String</span></span>                      | <span data-ttu-id="f33f3-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="f33f3-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="f33f3-212">`info` **lub** `information`</span><span class="sxs-lookup"><span data-stu-id="f33f3-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="f33f3-213">`warn` **lub** `warning`</span><span class="sxs-lookup"><span data-stu-id="f33f3-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="f33f3-214">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f33f3-215">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f33f3-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f33f3-216">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f33f3-217">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f33f3-218">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="f33f3-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f33f3-219">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="f33f3-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f33f3-220">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="f33f3-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f33f3-221">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="f33f3-221">Configure allowed transports</span></span>

<span data-ttu-id="f33f3-222">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f33f3-223">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f33f3-224">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-224">All transports are enabled by default.</span></span>

<span data-ttu-id="f33f3-225">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="f33f3-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f33f3-226">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f33f3-227">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="f33f3-228">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="f33f3-229">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f33f3-230">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f33f3-231">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="f33f3-231">Configure bearer authentication</span></span>

<span data-ttu-id="f33f3-232">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f33f3-233">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="f33f3-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f33f3-234">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="f33f3-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f33f3-235">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f33f3-236">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f33f3-237">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="f33f3-238">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="f33f3-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f33f3-239">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f33f3-240">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f33f3-241">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="f33f3-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f33f3-242">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-243">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-244">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-244">Option</span></span> | <span data-ttu-id="f33f3-245">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-245">Default value</span></span> | <span data-ttu-id="f33f3-246">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f33f3-247">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-248">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-248">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-249">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-250">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-251">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f33f3-252">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-252">15 seconds</span></span> | <span data-ttu-id="f33f3-253">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-254">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-255">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-256">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f33f3-257">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-257">15 seconds</span></span> | <span data-ttu-id="f33f3-258">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-259">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-260">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f33f3-261">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f33f3-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-263">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-263">Option</span></span> | <span data-ttu-id="f33f3-264">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-264">Default value</span></span> | <span data-ttu-id="f33f3-265">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f33f3-266">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-267">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-267">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-268">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f33f3-269">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-270">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f33f3-271">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-272">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-273">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-274">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-275">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-275">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-276">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-276">Option</span></span> | <span data-ttu-id="f33f3-277">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-277">Default value</span></span> | <span data-ttu-id="f33f3-278">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f33f3-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f33f3-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f33f3-280">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-281">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-281">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-282">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-283">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-284">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f33f3-285">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-285">15 seconds</span></span> | <span data-ttu-id="f33f3-286">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-287">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-288">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-289">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f33f3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f33f3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f33f3-291">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-292">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-293">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-294">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f33f3-295">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="f33f3-295">Configure additional options</span></span>

<span data-ttu-id="f33f3-296">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="f33f3-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-297">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-298">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-298">.NET Option</span></span> |  <span data-ttu-id="f33f3-299">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-299">Default value</span></span> | <span data-ttu-id="f33f3-300">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f33f3-301">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f33f3-302">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-303">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-304">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f33f3-305">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-305">Empty</span></span> | <span data-ttu-id="f33f3-306">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="f33f3-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="f33f3-307">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-307">Empty</span></span> | <span data-ttu-id="f33f3-308">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="f33f3-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f33f3-309">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-309">Empty</span></span> | <span data-ttu-id="f33f3-310">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f33f3-311">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-311">5 seconds</span></span> | <span data-ttu-id="f33f3-312">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-312">WebSockets only.</span></span> <span data-ttu-id="f33f3-313">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f33f3-314">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f33f3-315">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-315">Empty</span></span> | <span data-ttu-id="f33f3-316">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f33f3-317">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f33f3-318">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="f33f3-319">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="f33f3-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f33f3-320">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f33f3-321">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="f33f3-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f33f3-322">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f33f3-323">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f33f3-324">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="f33f3-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f33f3-325">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f33f3-326">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f33f3-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-328">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-328">JavaScript Option</span></span> | <span data-ttu-id="f33f3-329">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-329">Default Value</span></span> | <span data-ttu-id="f33f3-330">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f33f3-331">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="f33f3-332">Słownik nagłówków wysłanych z każdym żądaniem HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="f33f3-333">Wysyłanie nagłówków w przeglądarce nie działa w przypadku obiektów WebSockets ani <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> strumienia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f33f3-334">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f33f3-335">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-336">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-337">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="f33f3-338">Określa, czy poświadczenia będą wysyłane z żądaniem CORS.</span><span class="sxs-lookup"><span data-stu-id="f33f3-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="f33f3-339">Azure App Service używa cookie s dla sesji programu Sticky i wymaga, aby ta opcja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="f33f3-340">Aby uzyskać więcej informacji na temat mechanizmu CORS w programie SignalR , zobacz <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="f33f3-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-341">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-341">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-342">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-342">Java Option</span></span> | <span data-ttu-id="f33f3-343">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-343">Default Value</span></span> | <span data-ttu-id="f33f3-344">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f33f3-345">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f33f3-346">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-347">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-348">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="f33f3-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f33f3-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f33f3-350">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-350">Empty</span></span> | <span data-ttu-id="f33f3-351">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f33f3-352">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f33f3-353">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f33f3-354">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f33f3-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f33f3-355">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f33f3-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f33f3-356">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-357">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f33f3-358">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f33f3-359">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="f33f3-360">`AddJsonProtocol`można dodać po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do programu `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f33f3-361">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="f33f3-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f33f3-362">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f33f3-363">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="f33f3-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="f33f3-364">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="f33f3-365">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="f33f3-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f33f3-366">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f33f3-367">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="f33f3-368">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="f33f3-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="f33f3-369">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="f33f3-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f33f3-370">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-370">MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-371">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f33f3-372">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-373">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f33f3-374">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="f33f3-374">Configure server options</span></span>

<span data-ttu-id="f33f3-375">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="f33f3-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="f33f3-376">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-376">Option</span></span> | <span data-ttu-id="f33f3-377">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-377">Default Value</span></span> | <span data-ttu-id="f33f3-378">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f33f3-379">30 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-379">30 seconds</span></span> | <span data-ttu-id="f33f3-380">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="f33f3-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f33f3-381">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f33f3-382">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="f33f3-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f33f3-383">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-383">15 seconds</span></span> | <span data-ttu-id="f33f3-384">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="f33f3-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f33f3-385">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-386">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f33f3-387">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-387">15 seconds</span></span> | <span data-ttu-id="f33f3-388">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f33f3-389">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f33f3-390">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="f33f3-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f33f3-391">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="f33f3-391">All installed protocols</span></span> | <span data-ttu-id="f33f3-392">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-392">Protocols supported by this hub.</span></span> <span data-ttu-id="f33f3-393">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f33f3-394">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f33f3-395">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="f33f3-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="f33f3-396">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="f33f3-397">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="f33f3-398">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-398">32 KB</span></span> | <span data-ttu-id="f33f3-399">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="f33f3-400">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="f33f3-401">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f33f3-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f33f3-402">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="f33f3-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f33f3-403">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f33f3-404">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="f33f3-405">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f33f3-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="f33f3-406">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-406">Option</span></span> | <span data-ttu-id="f33f3-407">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-407">Default Value</span></span> | <span data-ttu-id="f33f3-408">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f33f3-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-409">32 KB</span></span> | <span data-ttu-id="f33f3-410">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="f33f3-411">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f33f3-412">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f33f3-413">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f33f3-414">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-414">32 KB</span></span> | <span data-ttu-id="f33f3-415">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="f33f3-416">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f33f3-417">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-417">All Transports are enabled.</span></span> | <span data-ttu-id="f33f3-418">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f33f3-419">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-419">See below.</span></span> | <span data-ttu-id="f33f3-420">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f33f3-421">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-421">See below.</span></span> | <span data-ttu-id="f33f3-422">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="f33f3-423">0</span><span class="sxs-lookup"><span data-stu-id="f33f3-423">0</span></span> | <span data-ttu-id="f33f3-424">Określ minimalną wersję protokołu negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="f33f3-425">Służy do ograniczania liczby klientów do nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="f33f3-426">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f33f3-427">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-427">Option</span></span> | <span data-ttu-id="f33f3-428">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-428">Default Value</span></span> | <span data-ttu-id="f33f3-429">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f33f3-430">90 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-430">90 seconds</span></span> | <span data-ttu-id="f33f3-431">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f33f3-432">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f33f3-433">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f33f3-434">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-434">Option</span></span> | <span data-ttu-id="f33f3-435">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-435">Default Value</span></span> | <span data-ttu-id="f33f3-436">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f33f3-437">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-437">5 seconds</span></span> | <span data-ttu-id="f33f3-438">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f33f3-439">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="f33f3-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f33f3-440">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f33f3-441">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="f33f3-441">Configure client options</span></span>

<span data-ttu-id="f33f3-442">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f33f3-443">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f33f3-444">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f33f3-444">Configure logging</span></span>

<span data-ttu-id="f33f3-445">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="f33f3-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f33f3-446">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="f33f3-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f33f3-447">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-448">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="f33f3-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f33f3-449">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="f33f3-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f33f3-450">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="f33f3-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f33f3-451">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f33f3-452">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="f33f3-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f33f3-453">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f33f3-454">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f33f3-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="f33f3-455">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="f33f3-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="f33f3-456">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="f33f3-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="f33f3-457">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="f33f3-457">The following table lists the available log levels.</span></span> <span data-ttu-id="f33f3-458">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="f33f3-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="f33f3-459">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="f33f3-460">String</span><span class="sxs-lookup"><span data-stu-id="f33f3-460">String</span></span>                      | <span data-ttu-id="f33f3-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="f33f3-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="f33f3-462">`info` **lub** `information`</span><span class="sxs-lookup"><span data-stu-id="f33f3-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="f33f3-463">`warn` **lub** `warning`</span><span class="sxs-lookup"><span data-stu-id="f33f3-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="f33f3-464">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f33f3-465">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f33f3-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f33f3-466">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f33f3-467">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f33f3-468">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="f33f3-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f33f3-469">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="f33f3-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f33f3-470">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="f33f3-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f33f3-471">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="f33f3-471">Configure allowed transports</span></span>

<span data-ttu-id="f33f3-472">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f33f3-473">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f33f3-474">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-474">All transports are enabled by default.</span></span>

<span data-ttu-id="f33f3-475">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="f33f3-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f33f3-476">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f33f3-477">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="f33f3-478">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="f33f3-479">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f33f3-480">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f33f3-481">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="f33f3-481">Configure bearer authentication</span></span>

<span data-ttu-id="f33f3-482">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f33f3-483">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="f33f3-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f33f3-484">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="f33f3-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f33f3-485">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f33f3-486">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f33f3-487">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="f33f3-488">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="f33f3-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f33f3-489">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f33f3-490">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f33f3-491">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="f33f3-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f33f3-492">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-493">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-494">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-494">Option</span></span> | <span data-ttu-id="f33f3-495">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-495">Default value</span></span> | <span data-ttu-id="f33f3-496">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f33f3-497">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-498">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-498">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-499">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-500">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-501">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f33f3-502">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-502">15 seconds</span></span> | <span data-ttu-id="f33f3-503">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-504">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-505">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-506">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f33f3-507">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-507">15 seconds</span></span> | <span data-ttu-id="f33f3-508">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-509">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-510">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f33f3-511">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f33f3-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-513">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-513">Option</span></span> | <span data-ttu-id="f33f3-514">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-514">Default value</span></span> | <span data-ttu-id="f33f3-515">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f33f3-516">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-517">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-517">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-518">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f33f3-519">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-520">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f33f3-521">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-522">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-523">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-524">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-525">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-525">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-526">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-526">Option</span></span> | <span data-ttu-id="f33f3-527">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-527">Default value</span></span> | <span data-ttu-id="f33f3-528">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f33f3-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f33f3-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f33f3-530">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-531">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-531">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-532">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-533">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-534">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f33f3-535">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-535">15 seconds</span></span> | <span data-ttu-id="f33f3-536">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-537">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-538">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-539">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f33f3-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f33f3-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f33f3-541">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-542">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-543">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-544">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f33f3-545">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="f33f3-545">Configure additional options</span></span>

<span data-ttu-id="f33f3-546">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="f33f3-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-547">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-548">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-548">.NET Option</span></span> |  <span data-ttu-id="f33f3-549">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-549">Default value</span></span> | <span data-ttu-id="f33f3-550">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f33f3-551">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f33f3-552">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-553">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-554">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f33f3-555">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-555">Empty</span></span> | <span data-ttu-id="f33f3-556">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="f33f3-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="f33f3-557">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-557">Empty</span></span> | <span data-ttu-id="f33f3-558">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="f33f3-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f33f3-559">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-559">Empty</span></span> | <span data-ttu-id="f33f3-560">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f33f3-561">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-561">5 seconds</span></span> | <span data-ttu-id="f33f3-562">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-562">WebSockets only.</span></span> <span data-ttu-id="f33f3-563">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f33f3-564">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f33f3-565">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-565">Empty</span></span> | <span data-ttu-id="f33f3-566">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f33f3-567">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f33f3-568">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="f33f3-569">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="f33f3-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f33f3-570">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f33f3-571">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="f33f3-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f33f3-572">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f33f3-573">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f33f3-574">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="f33f3-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f33f3-575">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f33f3-576">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f33f3-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-578">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-578">JavaScript Option</span></span> | <span data-ttu-id="f33f3-579">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-579">Default Value</span></span> | <span data-ttu-id="f33f3-580">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f33f3-581">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f33f3-582">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f33f3-583">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-584">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-585">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-586">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-586">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-587">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-587">Java Option</span></span> | <span data-ttu-id="f33f3-588">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-588">Default Value</span></span> | <span data-ttu-id="f33f3-589">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f33f3-590">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f33f3-591">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-592">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-593">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="f33f3-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f33f3-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f33f3-595">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-595">Empty</span></span> | <span data-ttu-id="f33f3-596">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f33f3-597">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f33f3-598">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f33f3-599">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f33f3-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f33f3-600">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f33f3-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f33f3-601">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-602">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f33f3-603">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f33f3-604">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="f33f3-605">`AddJsonProtocol`można dodać po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do programu `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f33f3-606">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="f33f3-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f33f3-607">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f33f3-608">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="f33f3-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="f33f3-609">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="f33f3-610">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="f33f3-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f33f3-611">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f33f3-612">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="f33f3-613">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="f33f3-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="f33f3-614">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="f33f3-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f33f3-615">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-615">MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-616">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f33f3-617">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-618">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f33f3-619">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="f33f3-619">Configure server options</span></span>

<span data-ttu-id="f33f3-620">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="f33f3-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="f33f3-621">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-621">Option</span></span> | <span data-ttu-id="f33f3-622">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-622">Default Value</span></span> | <span data-ttu-id="f33f3-623">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f33f3-624">30 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-624">30 seconds</span></span> | <span data-ttu-id="f33f3-625">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="f33f3-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f33f3-626">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f33f3-627">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="f33f3-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f33f3-628">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-628">15 seconds</span></span> | <span data-ttu-id="f33f3-629">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="f33f3-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f33f3-630">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-631">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f33f3-632">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-632">15 seconds</span></span> | <span data-ttu-id="f33f3-633">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f33f3-634">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f33f3-635">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="f33f3-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f33f3-636">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="f33f3-636">All installed protocols</span></span> | <span data-ttu-id="f33f3-637">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-637">Protocols supported by this hub.</span></span> <span data-ttu-id="f33f3-638">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f33f3-639">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f33f3-640">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="f33f3-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="f33f3-641">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="f33f3-642">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="f33f3-643">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-643">32 KB</span></span> | <span data-ttu-id="f33f3-644">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="f33f3-645">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="f33f3-646">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f33f3-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f33f3-647">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="f33f3-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f33f3-648">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f33f3-649">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="f33f3-650">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f33f3-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="f33f3-651">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-651">Option</span></span> | <span data-ttu-id="f33f3-652">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-652">Default Value</span></span> | <span data-ttu-id="f33f3-653">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f33f3-654">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-654">32 KB</span></span> | <span data-ttu-id="f33f3-655">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="f33f3-656">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f33f3-657">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f33f3-658">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f33f3-659">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-659">32 KB</span></span> | <span data-ttu-id="f33f3-660">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="f33f3-661">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f33f3-662">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-662">All Transports are enabled.</span></span> | <span data-ttu-id="f33f3-663">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f33f3-664">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-664">See below.</span></span> | <span data-ttu-id="f33f3-665">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f33f3-666">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-666">See below.</span></span> | <span data-ttu-id="f33f3-667">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="f33f3-668">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f33f3-669">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-669">Option</span></span> | <span data-ttu-id="f33f3-670">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-670">Default Value</span></span> | <span data-ttu-id="f33f3-671">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f33f3-672">90 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-672">90 seconds</span></span> | <span data-ttu-id="f33f3-673">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f33f3-674">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f33f3-675">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f33f3-676">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-676">Option</span></span> | <span data-ttu-id="f33f3-677">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-677">Default Value</span></span> | <span data-ttu-id="f33f3-678">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f33f3-679">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-679">5 seconds</span></span> | <span data-ttu-id="f33f3-680">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f33f3-681">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="f33f3-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f33f3-682">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f33f3-683">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="f33f3-683">Configure client options</span></span>

<span data-ttu-id="f33f3-684">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f33f3-685">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f33f3-686">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f33f3-686">Configure logging</span></span>

<span data-ttu-id="f33f3-687">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="f33f3-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f33f3-688">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="f33f3-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f33f3-689">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-690">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="f33f3-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f33f3-691">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="f33f3-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f33f3-692">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="f33f3-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f33f3-693">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f33f3-694">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="f33f3-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f33f3-695">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f33f3-696">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f33f3-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="f33f3-697">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="f33f3-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="f33f3-698">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="f33f3-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="f33f3-699">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="f33f3-699">The following table lists the available log levels.</span></span> <span data-ttu-id="f33f3-700">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="f33f3-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="f33f3-701">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="f33f3-702">String</span><span class="sxs-lookup"><span data-stu-id="f33f3-702">String</span></span>                      | <span data-ttu-id="f33f3-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="f33f3-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="f33f3-704">`info` **lub** `information`</span><span class="sxs-lookup"><span data-stu-id="f33f3-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="f33f3-705">`warn` **lub** `warning`</span><span class="sxs-lookup"><span data-stu-id="f33f3-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="f33f3-706">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f33f3-707">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f33f3-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f33f3-708">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f33f3-709">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f33f3-710">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="f33f3-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f33f3-711">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="f33f3-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f33f3-712">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="f33f3-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f33f3-713">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="f33f3-713">Configure allowed transports</span></span>

<span data-ttu-id="f33f3-714">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f33f3-715">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f33f3-716">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-716">All transports are enabled by default.</span></span>

<span data-ttu-id="f33f3-717">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="f33f3-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f33f3-718">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f33f3-719">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="f33f3-720">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="f33f3-721">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f33f3-722">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f33f3-723">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="f33f3-723">Configure bearer authentication</span></span>

<span data-ttu-id="f33f3-724">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f33f3-725">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="f33f3-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f33f3-726">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="f33f3-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f33f3-727">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f33f3-728">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f33f3-729">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="f33f3-730">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="f33f3-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f33f3-731">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f33f3-732">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f33f3-733">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="f33f3-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f33f3-734">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-735">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-736">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-736">Option</span></span> | <span data-ttu-id="f33f3-737">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-737">Default value</span></span> | <span data-ttu-id="f33f3-738">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f33f3-739">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-740">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-740">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-741">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-742">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-743">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f33f3-744">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-744">15 seconds</span></span> | <span data-ttu-id="f33f3-745">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-746">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-747">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-748">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f33f3-749">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-749">15 seconds</span></span> | <span data-ttu-id="f33f3-750">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-751">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-752">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f33f3-753">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f33f3-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-755">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-755">Option</span></span> | <span data-ttu-id="f33f3-756">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-756">Default value</span></span> | <span data-ttu-id="f33f3-757">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f33f3-758">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-759">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-759">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-760">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f33f3-761">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-762">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f33f3-763">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-764">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-765">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-766">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-767">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-767">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-768">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-768">Option</span></span> | <span data-ttu-id="f33f3-769">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-769">Default value</span></span> | <span data-ttu-id="f33f3-770">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f33f3-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f33f3-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f33f3-772">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-773">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-773">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-774">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-775">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-776">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f33f3-777">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-777">15 seconds</span></span> | <span data-ttu-id="f33f3-778">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-779">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-780">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-781">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f33f3-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f33f3-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f33f3-783">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-784">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-785">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-786">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f33f3-787">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="f33f3-787">Configure additional options</span></span>

<span data-ttu-id="f33f3-788">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="f33f3-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-789">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-790">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-790">.NET Option</span></span> |  <span data-ttu-id="f33f3-791">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-791">Default value</span></span> | <span data-ttu-id="f33f3-792">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f33f3-793">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f33f3-794">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-795">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-796">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f33f3-797">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-797">Empty</span></span> | <span data-ttu-id="f33f3-798">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="f33f3-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="f33f3-799">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-799">Empty</span></span> | <span data-ttu-id="f33f3-800">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="f33f3-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f33f3-801">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-801">Empty</span></span> | <span data-ttu-id="f33f3-802">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f33f3-803">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-803">5 seconds</span></span> | <span data-ttu-id="f33f3-804">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-804">WebSockets only.</span></span> <span data-ttu-id="f33f3-805">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f33f3-806">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f33f3-807">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-807">Empty</span></span> | <span data-ttu-id="f33f3-808">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f33f3-809">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f33f3-810">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="f33f3-811">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="f33f3-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f33f3-812">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f33f3-813">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="f33f3-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f33f3-814">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f33f3-815">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f33f3-816">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="f33f3-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f33f3-817">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f33f3-818">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f33f3-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-820">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-820">JavaScript Option</span></span> | <span data-ttu-id="f33f3-821">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-821">Default Value</span></span> | <span data-ttu-id="f33f3-822">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f33f3-823">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f33f3-824">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f33f3-825">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-826">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-827">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-828">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-828">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-829">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-829">Java Option</span></span> | <span data-ttu-id="f33f3-830">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-830">Default Value</span></span> | <span data-ttu-id="f33f3-831">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f33f3-832">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f33f3-833">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-834">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-835">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="f33f3-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f33f3-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f33f3-837">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-837">Empty</span></span> | <span data-ttu-id="f33f3-838">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f33f3-839">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f33f3-840">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f33f3-841">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f33f3-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f33f3-842">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f33f3-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f33f3-843">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-844">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f33f3-845">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f33f3-846">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może być dodana po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="f33f3-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f33f3-847">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="f33f3-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f33f3-848">Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f33f3-849">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="f33f3-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="f33f3-850">Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="f33f3-851">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="f33f3-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f33f3-852">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f33f3-853">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f33f3-854">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-854">MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-855">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f33f3-856">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-857">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f33f3-858">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="f33f3-858">Configure server options</span></span>

<span data-ttu-id="f33f3-859">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="f33f3-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="f33f3-860">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-860">Option</span></span> | <span data-ttu-id="f33f3-861">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-861">Default Value</span></span> | <span data-ttu-id="f33f3-862">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f33f3-863">30 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-863">30 seconds</span></span> | <span data-ttu-id="f33f3-864">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="f33f3-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f33f3-865">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f33f3-866">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="f33f3-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f33f3-867">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-867">15 seconds</span></span> | <span data-ttu-id="f33f3-868">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="f33f3-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f33f3-869">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-870">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f33f3-871">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-871">15 seconds</span></span> | <span data-ttu-id="f33f3-872">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f33f3-873">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f33f3-874">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="f33f3-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f33f3-875">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="f33f3-875">All installed protocols</span></span> | <span data-ttu-id="f33f3-876">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-876">Protocols supported by this hub.</span></span> <span data-ttu-id="f33f3-877">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f33f3-878">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f33f3-879">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="f33f3-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="f33f3-880">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="f33f3-881">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f33f3-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f33f3-882">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="f33f3-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f33f3-883">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f33f3-884">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="f33f3-885">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f33f3-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="f33f3-886">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-886">Option</span></span> | <span data-ttu-id="f33f3-887">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-887">Default Value</span></span> | <span data-ttu-id="f33f3-888">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f33f3-889">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-889">32 KB</span></span> | <span data-ttu-id="f33f3-890">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer.</span><span class="sxs-lookup"><span data-stu-id="f33f3-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="f33f3-891">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f33f3-892">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f33f3-893">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f33f3-894">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-894">32 KB</span></span> | <span data-ttu-id="f33f3-895">Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer.</span><span class="sxs-lookup"><span data-stu-id="f33f3-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="f33f3-896">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f33f3-897">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-897">All Transports are enabled.</span></span> | <span data-ttu-id="f33f3-898">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f33f3-899">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-899">See below.</span></span> | <span data-ttu-id="f33f3-900">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f33f3-901">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-901">See below.</span></span> | <span data-ttu-id="f33f3-902">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="f33f3-903">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f33f3-904">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-904">Option</span></span> | <span data-ttu-id="f33f3-905">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-905">Default Value</span></span> | <span data-ttu-id="f33f3-906">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f33f3-907">90 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-907">90 seconds</span></span> | <span data-ttu-id="f33f3-908">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f33f3-909">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f33f3-910">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f33f3-911">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-911">Option</span></span> | <span data-ttu-id="f33f3-912">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-912">Default Value</span></span> | <span data-ttu-id="f33f3-913">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f33f3-914">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-914">5 seconds</span></span> | <span data-ttu-id="f33f3-915">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f33f3-916">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="f33f3-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f33f3-917">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f33f3-918">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="f33f3-918">Configure client options</span></span>

<span data-ttu-id="f33f3-919">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f33f3-920">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f33f3-921">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f33f3-921">Configure logging</span></span>

<span data-ttu-id="f33f3-922">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="f33f3-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f33f3-923">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="f33f3-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f33f3-924">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-925">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="f33f3-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f33f3-926">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="f33f3-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f33f3-927">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="f33f3-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f33f3-928">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f33f3-929">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="f33f3-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f33f3-930">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f33f3-931">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f33f3-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f33f3-932">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f33f3-933">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f33f3-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f33f3-934">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f33f3-935">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f33f3-936">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="f33f3-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f33f3-937">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="f33f3-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f33f3-938">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="f33f3-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f33f3-939">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="f33f3-939">Configure allowed transports</span></span>

<span data-ttu-id="f33f3-940">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f33f3-941">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f33f3-942">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-942">All transports are enabled by default.</span></span>

<span data-ttu-id="f33f3-943">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="f33f3-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f33f3-944">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f33f3-945">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f33f3-946">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="f33f3-946">Configure bearer authentication</span></span>

<span data-ttu-id="f33f3-947">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f33f3-948">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="f33f3-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f33f3-949">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="f33f3-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f33f3-950">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f33f3-951">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f33f3-952">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="f33f3-953">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="f33f3-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f33f3-954">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f33f3-955">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f33f3-956">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="f33f3-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f33f3-957">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-958">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-959">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-959">Option</span></span> | <span data-ttu-id="f33f3-960">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-960">Default value</span></span> | <span data-ttu-id="f33f3-961">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f33f3-962">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-963">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-963">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-964">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-965">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-966">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f33f3-967">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-967">15 seconds</span></span> | <span data-ttu-id="f33f3-968">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-969">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-970">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-971">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f33f3-972">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-972">15 seconds</span></span> | <span data-ttu-id="f33f3-973">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-974">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-975">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f33f3-976">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f33f3-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-978">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-978">Option</span></span> | <span data-ttu-id="f33f3-979">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-979">Default value</span></span> | <span data-ttu-id="f33f3-980">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f33f3-981">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-982">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-982">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-983">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f33f3-984">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-985">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f33f3-986">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-987">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-988">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-989">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-990">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-990">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-991">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-991">Option</span></span> | <span data-ttu-id="f33f3-992">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-992">Default value</span></span> | <span data-ttu-id="f33f3-993">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f33f3-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f33f3-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f33f3-995">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-996">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-996">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-997">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-998">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-999">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f33f3-1000">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-1000">15 seconds</span></span> | <span data-ttu-id="f33f3-1001">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-1002">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-1003">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-1004">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f33f3-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f33f3-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f33f3-1006">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-1007">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f33f3-1008">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f33f3-1009">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f33f3-1010">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="f33f3-1010">Configure additional options</span></span>

<span data-ttu-id="f33f3-1011">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-1013">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-1013">.NET Option</span></span> |  <span data-ttu-id="f33f3-1014">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1014">Default value</span></span> | <span data-ttu-id="f33f3-1015">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f33f3-1016">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f33f3-1017">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-1018">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-1019">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f33f3-1020">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1020">Empty</span></span> | <span data-ttu-id="f33f3-1021">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="f33f3-1022">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1022">Empty</span></span> | <span data-ttu-id="f33f3-1023">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f33f3-1024">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1024">Empty</span></span> | <span data-ttu-id="f33f3-1025">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f33f3-1026">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-1026">5 seconds</span></span> | <span data-ttu-id="f33f3-1027">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1027">WebSockets only.</span></span> <span data-ttu-id="f33f3-1028">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f33f3-1029">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f33f3-1030">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1030">Empty</span></span> | <span data-ttu-id="f33f3-1031">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f33f3-1032">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f33f3-1033">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="f33f3-1034">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f33f3-1035">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f33f3-1036">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="f33f3-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f33f3-1037">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f33f3-1038">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f33f3-1039">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f33f3-1040">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f33f3-1041">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f33f3-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-1043">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-1043">JavaScript Option</span></span> | <span data-ttu-id="f33f3-1044">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1044">Default Value</span></span> | <span data-ttu-id="f33f3-1045">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f33f3-1046">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f33f3-1047">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f33f3-1048">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-1049">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-1050">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-1051">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-1052">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-1052">Java Option</span></span> | <span data-ttu-id="f33f3-1053">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1053">Default Value</span></span> | <span data-ttu-id="f33f3-1054">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f33f3-1055">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f33f3-1056">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-1057">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-1058">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="f33f3-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f33f3-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f33f3-1060">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1060">Empty</span></span> | <span data-ttu-id="f33f3-1061">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f33f3-1062">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f33f3-1063">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f33f3-1064">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f33f3-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f33f3-1065">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f33f3-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f33f3-1066">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-1067">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f33f3-1068">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f33f3-1069">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może być dodana po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f33f3-1070">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f33f3-1071">Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f33f3-1072">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="f33f3-1073">Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="f33f3-1074">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f33f3-1075">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f33f3-1076">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f33f3-1077">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="f33f3-1077">MessagePack serialization options</span></span>

<span data-ttu-id="f33f3-1078">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f33f3-1079">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-1080">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f33f3-1081">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="f33f3-1081">Configure server options</span></span>

<span data-ttu-id="f33f3-1082">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="f33f3-1083">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-1083">Option</span></span> | <span data-ttu-id="f33f3-1084">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1084">Default Value</span></span> | <span data-ttu-id="f33f3-1085">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="f33f3-1086">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-1086">15 seconds</span></span> | <span data-ttu-id="f33f3-1087">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f33f3-1088">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-1089">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f33f3-1090">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-1090">15 seconds</span></span> | <span data-ttu-id="f33f3-1091">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f33f3-1092">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f33f3-1093">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f33f3-1094">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="f33f3-1094">All installed protocols</span></span> | <span data-ttu-id="f33f3-1095">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="f33f3-1096">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f33f3-1097">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f33f3-1098">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="f33f3-1099">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="f33f3-1100">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f33f3-1101">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="f33f3-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f33f3-1102">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f33f3-1103">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="f33f3-1104">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="f33f3-1105">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-1105">Option</span></span> | <span data-ttu-id="f33f3-1106">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1106">Default Value</span></span> | <span data-ttu-id="f33f3-1107">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f33f3-1108">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-1108">32 KB</span></span> | <span data-ttu-id="f33f3-1109">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="f33f3-1110">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f33f3-1111">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f33f3-1112">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f33f3-1113">32 KB</span><span class="sxs-lookup"><span data-stu-id="f33f3-1113">32 KB</span></span> | <span data-ttu-id="f33f3-1114">Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="f33f3-1115">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f33f3-1116">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1116">All Transports are enabled.</span></span> | <span data-ttu-id="f33f3-1117">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f33f3-1118">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1118">See below.</span></span> | <span data-ttu-id="f33f3-1119">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f33f3-1120">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1120">See below.</span></span> | <span data-ttu-id="f33f3-1121">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="f33f3-1122">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f33f3-1123">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-1123">Option</span></span> | <span data-ttu-id="f33f3-1124">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1124">Default Value</span></span> | <span data-ttu-id="f33f3-1125">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f33f3-1126">90 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-1126">90 seconds</span></span> | <span data-ttu-id="f33f3-1127">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f33f3-1128">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f33f3-1129">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f33f3-1130">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-1130">Option</span></span> | <span data-ttu-id="f33f3-1131">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1131">Default Value</span></span> | <span data-ttu-id="f33f3-1132">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f33f3-1133">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-1133">5 seconds</span></span> | <span data-ttu-id="f33f3-1134">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f33f3-1135">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f33f3-1136">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f33f3-1137">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="f33f3-1137">Configure client options</span></span>

<span data-ttu-id="f33f3-1138">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f33f3-1139">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f33f3-1140">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f33f3-1140">Configure logging</span></span>

<span data-ttu-id="f33f3-1141">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f33f3-1142">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f33f3-1143">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f33f3-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f33f3-1144">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f33f3-1145">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f33f3-1146">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f33f3-1147">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f33f3-1148">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f33f3-1149">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f33f3-1150">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f33f3-1151">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f33f3-1152">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f33f3-1153">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f33f3-1154">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f33f3-1155">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f33f3-1156">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f33f3-1157">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f33f3-1158">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1158">Configure allowed transports</span></span>

<span data-ttu-id="f33f3-1159">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f33f3-1160">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f33f3-1161">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="f33f3-1162">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f33f3-1163">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f33f3-1164">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="f33f3-1164">Configure bearer authentication</span></span>

<span data-ttu-id="f33f3-1165">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f33f3-1166">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f33f3-1167">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f33f3-1168">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f33f3-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f33f3-1169">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f33f3-1170">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="f33f3-1171">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f33f3-1172">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f33f3-1173">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f33f3-1174">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="f33f3-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f33f3-1175">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-1177">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-1177">Option</span></span> | <span data-ttu-id="f33f3-1178">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1178">Default value</span></span> | <span data-ttu-id="f33f3-1179">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f33f3-1180">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-1181">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1181">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-1182">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-1183">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-1184">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f33f3-1185">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-1185">15 seconds</span></span> | <span data-ttu-id="f33f3-1186">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-1187">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f33f3-1188">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-1189">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="f33f3-1190">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f33f3-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-1192">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-1192">Option</span></span> | <span data-ttu-id="f33f3-1193">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1193">Default value</span></span> | <span data-ttu-id="f33f3-1194">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f33f3-1195">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-1196">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1196">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-1197">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f33f3-1198">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-1199">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-1200">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-1201">Opcja</span><span class="sxs-lookup"><span data-stu-id="f33f3-1201">Option</span></span> | <span data-ttu-id="f33f3-1202">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1202">Default value</span></span> | <span data-ttu-id="f33f3-1203">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f33f3-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f33f3-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f33f3-1205">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="f33f3-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f33f3-1206">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1206">Timeout for server activity.</span></span> <span data-ttu-id="f33f3-1207">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-1208">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f33f3-1209">Zalecana wartość to liczba z co najmniej podwójną `KeepAliveInterval` wartością serwera, aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f33f3-1210">15 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-1210">15 seconds</span></span> | <span data-ttu-id="f33f3-1211">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="f33f3-1212">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f33f3-1213">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f33f3-1214">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f33f3-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f33f3-1215">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="f33f3-1215">Configure additional options</span></span>

<span data-ttu-id="f33f3-1216">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="f33f3-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f33f3-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f33f3-1218">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="f33f3-1218">.NET Option</span></span> |  <span data-ttu-id="f33f3-1219">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1219">Default value</span></span> | <span data-ttu-id="f33f3-1220">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f33f3-1221">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f33f3-1222">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-1223">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-1224">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f33f3-1225">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1225">Empty</span></span> | <span data-ttu-id="f33f3-1226">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="f33f3-1227">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1227">Empty</span></span> | <span data-ttu-id="f33f3-1228">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f33f3-1229">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1229">Empty</span></span> | <span data-ttu-id="f33f3-1230">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f33f3-1231">5 sekund</span><span class="sxs-lookup"><span data-stu-id="f33f3-1231">5 seconds</span></span> | <span data-ttu-id="f33f3-1232">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1232">WebSockets only.</span></span> <span data-ttu-id="f33f3-1233">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f33f3-1234">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f33f3-1235">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1235">Empty</span></span> | <span data-ttu-id="f33f3-1236">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f33f3-1237">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f33f3-1238">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="f33f3-1239">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f33f3-1240">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f33f3-1241">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="f33f3-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f33f3-1242">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f33f3-1243">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f33f3-1244">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f33f3-1245">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f33f3-1246">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f33f3-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f33f3-1248">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="f33f3-1248">JavaScript Option</span></span> | <span data-ttu-id="f33f3-1249">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1249">Default Value</span></span> | <span data-ttu-id="f33f3-1250">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f33f3-1251">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f33f3-1252">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f33f3-1253">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-1254">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-1255">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f33f3-1256">Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="f33f3-1257">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="f33f3-1257">Java Option</span></span> | <span data-ttu-id="f33f3-1258">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="f33f3-1258">Default Value</span></span> | <span data-ttu-id="f33f3-1259">Opis</span><span class="sxs-lookup"><span data-stu-id="f33f3-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f33f3-1260">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f33f3-1261">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f33f3-1262">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f33f3-1263">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="f33f3-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f33f3-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f33f3-1265">Pusty</span><span class="sxs-lookup"><span data-stu-id="f33f3-1265">Empty</span></span> | <span data-ttu-id="f33f3-1266">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f33f3-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f33f3-1267">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f33f3-1268">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f33f3-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f33f3-1269">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f33f3-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f33f3-1270">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f33f3-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
