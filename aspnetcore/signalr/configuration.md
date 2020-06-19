---
title: SignalRKonfiguracja ASP.NET Core
author: bradygaster
description: Dowiedz się, jak skonfigurować SignalR aplikacje ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 809bdc777b6307314a7bcde82ab5e0c6888db99b
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074463"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="6e6cb-103">SignalRKonfiguracja ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6e6cb-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e6cb-104">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-105">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e6cb-106">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e6cb-107">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6e6cb-108">`AddJsonProtocol`można dodać po elemencie [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e6cb-109">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e6cb-110">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e6cb-111">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6e6cb-112">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6e6cb-113">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e6cb-114">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e6cb-115">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6e6cb-116">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6e6cb-117">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e6cb-118">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-118">MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-119">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e6cb-120">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-121">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e6cb-122">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="6e6cb-122">Configure server options</span></span>

<span data-ttu-id="6e6cb-123">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e6cb-124">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-124">Option</span></span> | <span data-ttu-id="6e6cb-125">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-125">Default Value</span></span> | <span data-ttu-id="6e6cb-126">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6e6cb-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-127">30 seconds</span></span> | <span data-ttu-id="6e6cb-128">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6e6cb-129">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6e6cb-130">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-131">15 seconds</span></span> | <span data-ttu-id="6e6cb-132">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e6cb-133">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-134">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e6cb-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-135">15 seconds</span></span> | <span data-ttu-id="6e6cb-136">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e6cb-137">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e6cb-138">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e6cb-139">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="6e6cb-139">All installed protocols</span></span> | <span data-ttu-id="6e6cb-140">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-140">Protocols supported by this hub.</span></span> <span data-ttu-id="6e6cb-141">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e6cb-142">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e6cb-143">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6e6cb-144">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6e6cb-145">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6e6cb-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-146">32 KB</span></span> | <span data-ttu-id="6e6cb-147">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6e6cb-148">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e6cb-149">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e6cb-150">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="6e6cb-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e6cb-151">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e6cb-152">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e6cb-153">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e6cb-154">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-154">Option</span></span> | <span data-ttu-id="6e6cb-155">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-155">Default Value</span></span> | <span data-ttu-id="6e6cb-156">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e6cb-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-157">32 KB</span></span> | <span data-ttu-id="6e6cb-158">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6e6cb-159">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e6cb-160">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e6cb-161">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e6cb-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-162">32 KB</span></span> | <span data-ttu-id="6e6cb-163">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6e6cb-164">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e6cb-165">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-165">All Transports are enabled.</span></span> | <span data-ttu-id="6e6cb-166">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e6cb-167">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-167">See below.</span></span> | <span data-ttu-id="6e6cb-168">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e6cb-169">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-169">See below.</span></span> | <span data-ttu-id="6e6cb-170">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="6e6cb-171">0</span><span class="sxs-lookup"><span data-stu-id="6e6cb-171">0</span></span> | <span data-ttu-id="6e6cb-172">Określ minimalną wersję protokołu negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="6e6cb-173">Służy do ograniczania liczby klientów do nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="6e6cb-174">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e6cb-175">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-175">Option</span></span> | <span data-ttu-id="6e6cb-176">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-176">Default Value</span></span> | <span data-ttu-id="6e6cb-177">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e6cb-178">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-178">90 seconds</span></span> | <span data-ttu-id="6e6cb-179">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e6cb-180">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e6cb-181">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e6cb-182">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-182">Option</span></span> | <span data-ttu-id="6e6cb-183">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-183">Default Value</span></span> | <span data-ttu-id="6e6cb-184">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e6cb-185">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-185">5 seconds</span></span> | <span data-ttu-id="6e6cb-186">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e6cb-187">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e6cb-188">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e6cb-189">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="6e6cb-189">Configure client options</span></span>

<span data-ttu-id="6e6cb-190">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e6cb-191">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e6cb-192">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="6e6cb-192">Configure logging</span></span>

<span data-ttu-id="6e6cb-193">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e6cb-194">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e6cb-195">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-196">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e6cb-197">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e6cb-198">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e6cb-199">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e6cb-200">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e6cb-201">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e6cb-202">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6e6cb-203">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6e6cb-204">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6e6cb-205">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-205">The following table lists the available log levels.</span></span> <span data-ttu-id="6e6cb-206">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6e6cb-207">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6e6cb-208">Ciąg</span><span class="sxs-lookup"><span data-stu-id="6e6cb-208">String</span></span>                      | <span data-ttu-id="6e6cb-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6e6cb-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6e6cb-210">`info` **lub** `information`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6e6cb-211">`warn` **lub** `warning`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6e6cb-212">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e6cb-213">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e6cb-214">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e6cb-215">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e6cb-216">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e6cb-217">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e6cb-218">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e6cb-219">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-219">Configure allowed transports</span></span>

<span data-ttu-id="6e6cb-220">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e6cb-221">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e6cb-222">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-222">All transports are enabled by default.</span></span>

<span data-ttu-id="6e6cb-223">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e6cb-224">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6e6cb-225">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6e6cb-226">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6e6cb-227">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e6cb-228">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e6cb-229">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="6e6cb-229">Configure bearer authentication</span></span>

<span data-ttu-id="6e6cb-230">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e6cb-231">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e6cb-232">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e6cb-233">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e6cb-234">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e6cb-235">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e6cb-236">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e6cb-237">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e6cb-238">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e6cb-239">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="6e6cb-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e6cb-240">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-241">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-242">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-242">Option</span></span> | <span data-ttu-id="6e6cb-243">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-243">Default value</span></span> | <span data-ttu-id="6e6cb-244">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e6cb-245">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-246">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-246">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-247">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-248">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-249">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-250">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-250">15 seconds</span></span> | <span data-ttu-id="6e6cb-251">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-252">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-253">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-254">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e6cb-255">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-255">15 seconds</span></span> | <span data-ttu-id="6e6cb-256">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-257">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-258">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6e6cb-259">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-261">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-261">Option</span></span> | <span data-ttu-id="6e6cb-262">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-262">Default value</span></span> | <span data-ttu-id="6e6cb-263">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e6cb-264">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-265">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-265">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-266">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e6cb-267">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-268">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6e6cb-269">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-270">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-271">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-272">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-273">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-273">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-274">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-274">Option</span></span> | <span data-ttu-id="6e6cb-275">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-275">Default value</span></span> | <span data-ttu-id="6e6cb-276">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e6cb-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e6cb-278">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-279">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-279">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-280">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-281">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-282">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e6cb-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-283">15 seconds</span></span> | <span data-ttu-id="6e6cb-284">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-285">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-286">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-287">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6e6cb-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6e6cb-289">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-290">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-291">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-292">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e6cb-293">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="6e6cb-293">Configure additional options</span></span>

<span data-ttu-id="6e6cb-294">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-295">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-296">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-296">.NET Option</span></span> |  <span data-ttu-id="6e6cb-297">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-297">Default value</span></span> | <span data-ttu-id="6e6cb-298">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-299">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e6cb-300">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-301">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-302">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e6cb-303">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-303">Empty</span></span> | <span data-ttu-id="6e6cb-304">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e6cb-305">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-305">Empty</span></span> | <span data-ttu-id="6e6cb-306">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e6cb-307">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-307">Empty</span></span> | <span data-ttu-id="6e6cb-308">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e6cb-309">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-309">5 seconds</span></span> | <span data-ttu-id="6e6cb-310">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-310">WebSockets only.</span></span> <span data-ttu-id="6e6cb-311">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e6cb-312">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e6cb-313">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-313">Empty</span></span> | <span data-ttu-id="6e6cb-314">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e6cb-315">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e6cb-316">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e6cb-317">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e6cb-318">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e6cb-319">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="6e6cb-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e6cb-320">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e6cb-321">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e6cb-322">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e6cb-323">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e6cb-324">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-326">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-326">JavaScript Option</span></span> | <span data-ttu-id="6e6cb-327">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-327">Default Value</span></span> | <span data-ttu-id="6e6cb-328">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e6cb-329">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="6e6cb-330">Słownik nagłówków wysłanych z każdym żądaniem HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="6e6cb-331">Wysyłanie nagłówków w przeglądarce nie działa w przypadku obiektów WebSockets ani <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> strumienia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6e6cb-332">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e6cb-333">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-334">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-335">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="6e6cb-336">Określa, czy poświadczenia będą wysyłane z żądaniem CORS.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="6e6cb-337">Azure App Service używa plików cookie dla sesji programu Sticky i wymaga, aby ta opcja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="6e6cb-338">Aby uzyskać więcej informacji na temat mechanizmu CORS w programie SignalR , zobacz <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-339">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-339">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-340">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-340">Java Option</span></span> | <span data-ttu-id="6e6cb-341">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-341">Default Value</span></span> | <span data-ttu-id="6e6cb-342">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-343">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e6cb-344">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-345">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-346">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e6cb-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e6cb-348">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-348">Empty</span></span> | <span data-ttu-id="6e6cb-349">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e6cb-350">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e6cb-351">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e6cb-352">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e6cb-353">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6e6cb-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e6cb-354">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-355">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e6cb-356">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e6cb-357">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6e6cb-358">`AddJsonProtocol`można dodać po elemencie [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e6cb-359">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e6cb-360">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e6cb-361">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6e6cb-362">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6e6cb-363">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e6cb-364">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e6cb-365">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6e6cb-366">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6e6cb-367">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e6cb-368">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-368">MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-369">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e6cb-370">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-371">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e6cb-372">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="6e6cb-372">Configure server options</span></span>

<span data-ttu-id="6e6cb-373">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e6cb-374">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-374">Option</span></span> | <span data-ttu-id="6e6cb-375">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-375">Default Value</span></span> | <span data-ttu-id="6e6cb-376">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6e6cb-377">30 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-377">30 seconds</span></span> | <span data-ttu-id="6e6cb-378">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6e6cb-379">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6e6cb-380">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-381">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-381">15 seconds</span></span> | <span data-ttu-id="6e6cb-382">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e6cb-383">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-384">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e6cb-385">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-385">15 seconds</span></span> | <span data-ttu-id="6e6cb-386">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e6cb-387">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e6cb-388">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e6cb-389">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="6e6cb-389">All installed protocols</span></span> | <span data-ttu-id="6e6cb-390">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-390">Protocols supported by this hub.</span></span> <span data-ttu-id="6e6cb-391">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e6cb-392">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e6cb-393">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6e6cb-394">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6e6cb-395">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6e6cb-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-396">32 KB</span></span> | <span data-ttu-id="6e6cb-397">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6e6cb-398">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e6cb-399">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e6cb-400">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="6e6cb-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e6cb-401">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e6cb-402">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e6cb-403">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e6cb-404">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-404">Option</span></span> | <span data-ttu-id="6e6cb-405">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-405">Default Value</span></span> | <span data-ttu-id="6e6cb-406">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e6cb-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-407">32 KB</span></span> | <span data-ttu-id="6e6cb-408">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6e6cb-409">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e6cb-410">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e6cb-411">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e6cb-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-412">32 KB</span></span> | <span data-ttu-id="6e6cb-413">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6e6cb-414">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e6cb-415">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-415">All Transports are enabled.</span></span> | <span data-ttu-id="6e6cb-416">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e6cb-417">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-417">See below.</span></span> | <span data-ttu-id="6e6cb-418">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e6cb-419">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-419">See below.</span></span> | <span data-ttu-id="6e6cb-420">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="6e6cb-421">0</span><span class="sxs-lookup"><span data-stu-id="6e6cb-421">0</span></span> | <span data-ttu-id="6e6cb-422">Określ minimalną wersję protokołu negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="6e6cb-423">Służy do ograniczania liczby klientów do nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="6e6cb-424">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e6cb-425">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-425">Option</span></span> | <span data-ttu-id="6e6cb-426">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-426">Default Value</span></span> | <span data-ttu-id="6e6cb-427">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e6cb-428">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-428">90 seconds</span></span> | <span data-ttu-id="6e6cb-429">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e6cb-430">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e6cb-431">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e6cb-432">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-432">Option</span></span> | <span data-ttu-id="6e6cb-433">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-433">Default Value</span></span> | <span data-ttu-id="6e6cb-434">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e6cb-435">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-435">5 seconds</span></span> | <span data-ttu-id="6e6cb-436">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e6cb-437">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e6cb-438">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e6cb-439">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="6e6cb-439">Configure client options</span></span>

<span data-ttu-id="6e6cb-440">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e6cb-441">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e6cb-442">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="6e6cb-442">Configure logging</span></span>

<span data-ttu-id="6e6cb-443">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e6cb-444">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e6cb-445">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-446">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e6cb-447">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e6cb-448">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e6cb-449">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e6cb-450">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e6cb-451">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e6cb-452">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6e6cb-453">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6e6cb-454">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6e6cb-455">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-455">The following table lists the available log levels.</span></span> <span data-ttu-id="6e6cb-456">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6e6cb-457">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6e6cb-458">Ciąg</span><span class="sxs-lookup"><span data-stu-id="6e6cb-458">String</span></span>                      | <span data-ttu-id="6e6cb-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6e6cb-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6e6cb-460">`info` **lub** `information`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6e6cb-461">`warn` **lub** `warning`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6e6cb-462">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e6cb-463">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e6cb-464">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e6cb-465">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e6cb-466">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e6cb-467">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e6cb-468">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e6cb-469">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-469">Configure allowed transports</span></span>

<span data-ttu-id="6e6cb-470">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e6cb-471">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e6cb-472">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-472">All transports are enabled by default.</span></span>

<span data-ttu-id="6e6cb-473">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e6cb-474">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6e6cb-475">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6e6cb-476">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6e6cb-477">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e6cb-478">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e6cb-479">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="6e6cb-479">Configure bearer authentication</span></span>

<span data-ttu-id="6e6cb-480">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e6cb-481">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e6cb-482">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e6cb-483">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e6cb-484">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e6cb-485">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e6cb-486">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e6cb-487">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e6cb-488">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e6cb-489">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="6e6cb-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e6cb-490">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-491">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-492">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-492">Option</span></span> | <span data-ttu-id="6e6cb-493">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-493">Default value</span></span> | <span data-ttu-id="6e6cb-494">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e6cb-495">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-496">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-496">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-497">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-498">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-499">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-500">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-500">15 seconds</span></span> | <span data-ttu-id="6e6cb-501">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-502">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-503">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-504">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e6cb-505">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-505">15 seconds</span></span> | <span data-ttu-id="6e6cb-506">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-507">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-508">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6e6cb-509">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-511">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-511">Option</span></span> | <span data-ttu-id="6e6cb-512">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-512">Default value</span></span> | <span data-ttu-id="6e6cb-513">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e6cb-514">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-515">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-515">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-516">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e6cb-517">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-518">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6e6cb-519">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-520">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-521">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-522">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-523">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-523">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-524">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-524">Option</span></span> | <span data-ttu-id="6e6cb-525">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-525">Default value</span></span> | <span data-ttu-id="6e6cb-526">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e6cb-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e6cb-528">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-529">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-529">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-530">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-531">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-532">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e6cb-533">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-533">15 seconds</span></span> | <span data-ttu-id="6e6cb-534">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-535">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-536">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-537">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6e6cb-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6e6cb-539">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-540">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-541">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-542">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e6cb-543">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="6e6cb-543">Configure additional options</span></span>

<span data-ttu-id="6e6cb-544">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-545">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-546">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-546">.NET Option</span></span> |  <span data-ttu-id="6e6cb-547">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-547">Default value</span></span> | <span data-ttu-id="6e6cb-548">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-549">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e6cb-550">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-551">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-552">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e6cb-553">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-553">Empty</span></span> | <span data-ttu-id="6e6cb-554">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e6cb-555">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-555">Empty</span></span> | <span data-ttu-id="6e6cb-556">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e6cb-557">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-557">Empty</span></span> | <span data-ttu-id="6e6cb-558">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e6cb-559">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-559">5 seconds</span></span> | <span data-ttu-id="6e6cb-560">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-560">WebSockets only.</span></span> <span data-ttu-id="6e6cb-561">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e6cb-562">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e6cb-563">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-563">Empty</span></span> | <span data-ttu-id="6e6cb-564">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e6cb-565">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e6cb-566">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e6cb-567">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e6cb-568">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e6cb-569">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="6e6cb-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e6cb-570">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e6cb-571">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e6cb-572">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e6cb-573">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e6cb-574">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-576">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-576">JavaScript Option</span></span> | <span data-ttu-id="6e6cb-577">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-577">Default Value</span></span> | <span data-ttu-id="6e6cb-578">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e6cb-579">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6e6cb-580">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e6cb-581">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-582">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-583">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-584">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-584">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-585">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-585">Java Option</span></span> | <span data-ttu-id="6e6cb-586">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-586">Default Value</span></span> | <span data-ttu-id="6e6cb-587">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-588">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e6cb-589">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-590">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-591">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e6cb-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e6cb-593">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-593">Empty</span></span> | <span data-ttu-id="6e6cb-594">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e6cb-595">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e6cb-596">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e6cb-597">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e6cb-598">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6e6cb-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e6cb-599">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-600">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e6cb-601">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e6cb-602">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6e6cb-603">`AddJsonProtocol`można dodać po elemencie [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e6cb-604">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e6cb-605">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e6cb-606">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6e6cb-607">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="6e6cb-608">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e6cb-609">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e6cb-610">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6e6cb-611">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6e6cb-612">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e6cb-613">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-613">MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-614">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e6cb-615">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-616">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e6cb-617">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="6e6cb-617">Configure server options</span></span>

<span data-ttu-id="6e6cb-618">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e6cb-619">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-619">Option</span></span> | <span data-ttu-id="6e6cb-620">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-620">Default Value</span></span> | <span data-ttu-id="6e6cb-621">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6e6cb-622">30 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-622">30 seconds</span></span> | <span data-ttu-id="6e6cb-623">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6e6cb-624">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6e6cb-625">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-626">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-626">15 seconds</span></span> | <span data-ttu-id="6e6cb-627">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e6cb-628">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-629">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e6cb-630">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-630">15 seconds</span></span> | <span data-ttu-id="6e6cb-631">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e6cb-632">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e6cb-633">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e6cb-634">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="6e6cb-634">All installed protocols</span></span> | <span data-ttu-id="6e6cb-635">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-635">Protocols supported by this hub.</span></span> <span data-ttu-id="6e6cb-636">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e6cb-637">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e6cb-638">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6e6cb-639">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6e6cb-640">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6e6cb-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-641">32 KB</span></span> | <span data-ttu-id="6e6cb-642">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6e6cb-643">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e6cb-644">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e6cb-645">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="6e6cb-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e6cb-646">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e6cb-647">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e6cb-648">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e6cb-649">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-649">Option</span></span> | <span data-ttu-id="6e6cb-650">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-650">Default Value</span></span> | <span data-ttu-id="6e6cb-651">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e6cb-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-652">32 KB</span></span> | <span data-ttu-id="6e6cb-653">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6e6cb-654">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e6cb-655">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e6cb-656">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e6cb-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-657">32 KB</span></span> | <span data-ttu-id="6e6cb-658">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6e6cb-659">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e6cb-660">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-660">All Transports are enabled.</span></span> | <span data-ttu-id="6e6cb-661">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e6cb-662">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-662">See below.</span></span> | <span data-ttu-id="6e6cb-663">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e6cb-664">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-664">See below.</span></span> | <span data-ttu-id="6e6cb-665">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6e6cb-666">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e6cb-667">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-667">Option</span></span> | <span data-ttu-id="6e6cb-668">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-668">Default Value</span></span> | <span data-ttu-id="6e6cb-669">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e6cb-670">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-670">90 seconds</span></span> | <span data-ttu-id="6e6cb-671">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e6cb-672">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e6cb-673">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e6cb-674">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-674">Option</span></span> | <span data-ttu-id="6e6cb-675">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-675">Default Value</span></span> | <span data-ttu-id="6e6cb-676">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e6cb-677">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-677">5 seconds</span></span> | <span data-ttu-id="6e6cb-678">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e6cb-679">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e6cb-680">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e6cb-681">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="6e6cb-681">Configure client options</span></span>

<span data-ttu-id="6e6cb-682">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e6cb-683">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e6cb-684">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="6e6cb-684">Configure logging</span></span>

<span data-ttu-id="6e6cb-685">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e6cb-686">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e6cb-687">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-688">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e6cb-689">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e6cb-690">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e6cb-691">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e6cb-692">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e6cb-693">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e6cb-694">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6e6cb-695">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6e6cb-696">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6e6cb-697">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-697">The following table lists the available log levels.</span></span> <span data-ttu-id="6e6cb-698">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6e6cb-699">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6e6cb-700">Ciąg</span><span class="sxs-lookup"><span data-stu-id="6e6cb-700">String</span></span>                      | <span data-ttu-id="6e6cb-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6e6cb-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6e6cb-702">`info` **lub** `information`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6e6cb-703">`warn` **lub** `warning`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6e6cb-704">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e6cb-705">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e6cb-706">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e6cb-707">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e6cb-708">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e6cb-709">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e6cb-710">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e6cb-711">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-711">Configure allowed transports</span></span>

<span data-ttu-id="6e6cb-712">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e6cb-713">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e6cb-714">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-714">All transports are enabled by default.</span></span>

<span data-ttu-id="6e6cb-715">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e6cb-716">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6e6cb-717">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6e6cb-718">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6e6cb-719">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e6cb-720">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e6cb-721">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="6e6cb-721">Configure bearer authentication</span></span>

<span data-ttu-id="6e6cb-722">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e6cb-723">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e6cb-724">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e6cb-725">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e6cb-726">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e6cb-727">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e6cb-728">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e6cb-729">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e6cb-730">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e6cb-731">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="6e6cb-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e6cb-732">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-733">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-734">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-734">Option</span></span> | <span data-ttu-id="6e6cb-735">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-735">Default value</span></span> | <span data-ttu-id="6e6cb-736">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e6cb-737">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-738">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-738">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-739">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-740">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-741">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-742">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-742">15 seconds</span></span> | <span data-ttu-id="6e6cb-743">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-744">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-745">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-746">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e6cb-747">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-747">15 seconds</span></span> | <span data-ttu-id="6e6cb-748">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-749">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-750">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6e6cb-751">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-753">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-753">Option</span></span> | <span data-ttu-id="6e6cb-754">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-754">Default value</span></span> | <span data-ttu-id="6e6cb-755">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e6cb-756">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-757">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-757">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-758">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e6cb-759">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-760">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6e6cb-761">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-762">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-763">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-764">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-765">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-765">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-766">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-766">Option</span></span> | <span data-ttu-id="6e6cb-767">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-767">Default value</span></span> | <span data-ttu-id="6e6cb-768">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e6cb-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e6cb-770">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-771">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-771">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-772">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-773">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-774">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e6cb-775">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-775">15 seconds</span></span> | <span data-ttu-id="6e6cb-776">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-777">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-778">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-779">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6e6cb-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6e6cb-781">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-782">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-783">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-784">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e6cb-785">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="6e6cb-785">Configure additional options</span></span>

<span data-ttu-id="6e6cb-786">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-787">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-788">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-788">.NET Option</span></span> |  <span data-ttu-id="6e6cb-789">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-789">Default value</span></span> | <span data-ttu-id="6e6cb-790">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-791">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e6cb-792">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-793">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-794">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e6cb-795">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-795">Empty</span></span> | <span data-ttu-id="6e6cb-796">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e6cb-797">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-797">Empty</span></span> | <span data-ttu-id="6e6cb-798">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e6cb-799">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-799">Empty</span></span> | <span data-ttu-id="6e6cb-800">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e6cb-801">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-801">5 seconds</span></span> | <span data-ttu-id="6e6cb-802">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-802">WebSockets only.</span></span> <span data-ttu-id="6e6cb-803">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e6cb-804">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e6cb-805">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-805">Empty</span></span> | <span data-ttu-id="6e6cb-806">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e6cb-807">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e6cb-808">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e6cb-809">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e6cb-810">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e6cb-811">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="6e6cb-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e6cb-812">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e6cb-813">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e6cb-814">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e6cb-815">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e6cb-816">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-818">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-818">JavaScript Option</span></span> | <span data-ttu-id="6e6cb-819">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-819">Default Value</span></span> | <span data-ttu-id="6e6cb-820">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e6cb-821">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6e6cb-822">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e6cb-823">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-824">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-825">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-826">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-826">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-827">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-827">Java Option</span></span> | <span data-ttu-id="6e6cb-828">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-828">Default Value</span></span> | <span data-ttu-id="6e6cb-829">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-830">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e6cb-831">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-832">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-833">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e6cb-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e6cb-835">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-835">Empty</span></span> | <span data-ttu-id="6e6cb-836">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e6cb-837">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e6cb-838">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e6cb-839">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e6cb-840">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6e6cb-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e6cb-841">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-842">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e6cb-843">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e6cb-844">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może zostać dodana po metodzie [addsygnalizująca](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w ramach `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6e6cb-845">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e6cb-846">Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e6cb-847">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6e6cb-848">Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6e6cb-849">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e6cb-850">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e6cb-851">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e6cb-852">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-852">MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-853">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e6cb-854">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-855">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e6cb-856">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="6e6cb-856">Configure server options</span></span>

<span data-ttu-id="6e6cb-857">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e6cb-858">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-858">Option</span></span> | <span data-ttu-id="6e6cb-859">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-859">Default Value</span></span> | <span data-ttu-id="6e6cb-860">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6e6cb-861">30 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-861">30 seconds</span></span> | <span data-ttu-id="6e6cb-862">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6e6cb-863">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6e6cb-864">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-865">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-865">15 seconds</span></span> | <span data-ttu-id="6e6cb-866">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e6cb-867">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-868">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e6cb-869">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-869">15 seconds</span></span> | <span data-ttu-id="6e6cb-870">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e6cb-871">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e6cb-872">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e6cb-873">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="6e6cb-873">All installed protocols</span></span> | <span data-ttu-id="6e6cb-874">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-874">Protocols supported by this hub.</span></span> <span data-ttu-id="6e6cb-875">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e6cb-876">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e6cb-877">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6e6cb-878">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e6cb-879">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e6cb-880">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="6e6cb-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e6cb-881">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e6cb-882">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e6cb-883">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e6cb-884">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-884">Option</span></span> | <span data-ttu-id="6e6cb-885">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-885">Default Value</span></span> | <span data-ttu-id="6e6cb-886">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e6cb-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-887">32 KB</span></span> | <span data-ttu-id="6e6cb-888">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6e6cb-889">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e6cb-890">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e6cb-891">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e6cb-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-892">32 KB</span></span> | <span data-ttu-id="6e6cb-893">Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6e6cb-894">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e6cb-895">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-895">All Transports are enabled.</span></span> | <span data-ttu-id="6e6cb-896">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e6cb-897">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-897">See below.</span></span> | <span data-ttu-id="6e6cb-898">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e6cb-899">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-899">See below.</span></span> | <span data-ttu-id="6e6cb-900">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6e6cb-901">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e6cb-902">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-902">Option</span></span> | <span data-ttu-id="6e6cb-903">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-903">Default Value</span></span> | <span data-ttu-id="6e6cb-904">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e6cb-905">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-905">90 seconds</span></span> | <span data-ttu-id="6e6cb-906">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e6cb-907">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e6cb-908">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e6cb-909">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-909">Option</span></span> | <span data-ttu-id="6e6cb-910">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-910">Default Value</span></span> | <span data-ttu-id="6e6cb-911">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e6cb-912">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-912">5 seconds</span></span> | <span data-ttu-id="6e6cb-913">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e6cb-914">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e6cb-915">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e6cb-916">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="6e6cb-916">Configure client options</span></span>

<span data-ttu-id="6e6cb-917">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e6cb-918">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e6cb-919">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="6e6cb-919">Configure logging</span></span>

<span data-ttu-id="6e6cb-920">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e6cb-921">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e6cb-922">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-923">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e6cb-924">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e6cb-925">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e6cb-926">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e6cb-927">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e6cb-928">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e6cb-929">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e6cb-930">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e6cb-931">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e6cb-932">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e6cb-933">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e6cb-934">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e6cb-935">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e6cb-936">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e6cb-937">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-937">Configure allowed transports</span></span>

<span data-ttu-id="6e6cb-938">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e6cb-939">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e6cb-940">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-940">All transports are enabled by default.</span></span>

<span data-ttu-id="6e6cb-941">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e6cb-942">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6e6cb-943">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e6cb-944">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="6e6cb-944">Configure bearer authentication</span></span>

<span data-ttu-id="6e6cb-945">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e6cb-946">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e6cb-947">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e6cb-948">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e6cb-949">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e6cb-950">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e6cb-951">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e6cb-952">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e6cb-953">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e6cb-954">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="6e6cb-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e6cb-955">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-956">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-957">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-957">Option</span></span> | <span data-ttu-id="6e6cb-958">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-958">Default value</span></span> | <span data-ttu-id="6e6cb-959">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e6cb-960">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-961">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-961">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-962">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-963">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-964">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-965">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-965">15 seconds</span></span> | <span data-ttu-id="6e6cb-966">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-967">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-968">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-969">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e6cb-970">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-970">15 seconds</span></span> | <span data-ttu-id="6e6cb-971">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-972">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-973">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6e6cb-974">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-976">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-976">Option</span></span> | <span data-ttu-id="6e6cb-977">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-977">Default value</span></span> | <span data-ttu-id="6e6cb-978">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e6cb-979">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-980">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-980">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-981">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e6cb-982">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-983">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6e6cb-984">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-985">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-986">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-987">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-988">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-988">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-989">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-989">Option</span></span> | <span data-ttu-id="6e6cb-990">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-990">Default value</span></span> | <span data-ttu-id="6e6cb-991">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e6cb-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e6cb-993">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-994">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-994">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-995">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-996">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-997">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e6cb-998">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-998">15 seconds</span></span> | <span data-ttu-id="6e6cb-999">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-1000">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-1001">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-1002">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6e6cb-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6e6cb-1004">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-1005">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e6cb-1006">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e6cb-1007">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e6cb-1008">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1008">Configure additional options</span></span>

<span data-ttu-id="6e6cb-1009">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-1011">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1011">.NET Option</span></span> |  <span data-ttu-id="6e6cb-1012">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1012">Default value</span></span> | <span data-ttu-id="6e6cb-1013">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-1014">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e6cb-1015">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-1016">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-1017">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e6cb-1018">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1018">Empty</span></span> | <span data-ttu-id="6e6cb-1019">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e6cb-1020">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1020">Empty</span></span> | <span data-ttu-id="6e6cb-1021">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e6cb-1022">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1022">Empty</span></span> | <span data-ttu-id="6e6cb-1023">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e6cb-1024">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1024">5 seconds</span></span> | <span data-ttu-id="6e6cb-1025">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1025">WebSockets only.</span></span> <span data-ttu-id="6e6cb-1026">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e6cb-1027">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e6cb-1028">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1028">Empty</span></span> | <span data-ttu-id="6e6cb-1029">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e6cb-1030">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e6cb-1031">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e6cb-1032">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e6cb-1033">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e6cb-1034">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e6cb-1035">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e6cb-1036">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e6cb-1037">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e6cb-1038">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e6cb-1039">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-1041">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1041">JavaScript Option</span></span> | <span data-ttu-id="6e6cb-1042">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1042">Default Value</span></span> | <span data-ttu-id="6e6cb-1043">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e6cb-1044">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6e6cb-1045">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e6cb-1046">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-1047">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-1048">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-1049">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-1050">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1050">Java Option</span></span> | <span data-ttu-id="6e6cb-1051">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1051">Default Value</span></span> | <span data-ttu-id="6e6cb-1052">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-1053">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e6cb-1054">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-1055">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-1056">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e6cb-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e6cb-1058">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1058">Empty</span></span> | <span data-ttu-id="6e6cb-1059">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e6cb-1060">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e6cb-1061">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e6cb-1062">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e6cb-1063">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e6cb-1064">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-1065">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e6cb-1066">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e6cb-1067">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może zostać dodana po metodzie [addsygnalizująca](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w ramach `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6e6cb-1068">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e6cb-1069">Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e6cb-1070">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6e6cb-1071">Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6e6cb-1072">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e6cb-1073">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e6cb-1074">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e6cb-1075">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1075">MessagePack serialization options</span></span>

<span data-ttu-id="6e6cb-1076">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e6cb-1077">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-1078">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e6cb-1079">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1079">Configure server options</span></span>

<span data-ttu-id="6e6cb-1080">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e6cb-1081">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1081">Option</span></span> | <span data-ttu-id="6e6cb-1082">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1082">Default Value</span></span> | <span data-ttu-id="6e6cb-1083">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-1084">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1084">15 seconds</span></span> | <span data-ttu-id="6e6cb-1085">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e6cb-1086">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-1087">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e6cb-1088">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1088">15 seconds</span></span> | <span data-ttu-id="6e6cb-1089">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e6cb-1090">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e6cb-1091">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e6cb-1092">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1092">All installed protocols</span></span> | <span data-ttu-id="6e6cb-1093">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="6e6cb-1094">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e6cb-1095">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e6cb-1096">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6e6cb-1097">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e6cb-1098">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e6cb-1099">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e6cb-1100">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e6cb-1101">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e6cb-1102">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e6cb-1103">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1103">Option</span></span> | <span data-ttu-id="6e6cb-1104">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1104">Default Value</span></span> | <span data-ttu-id="6e6cb-1105">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e6cb-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1106">32 KB</span></span> | <span data-ttu-id="6e6cb-1107">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6e6cb-1108">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e6cb-1109">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e6cb-1110">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e6cb-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1111">32 KB</span></span> | <span data-ttu-id="6e6cb-1112">Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6e6cb-1113">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e6cb-1114">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1114">All Transports are enabled.</span></span> | <span data-ttu-id="6e6cb-1115">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e6cb-1116">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1116">See below.</span></span> | <span data-ttu-id="6e6cb-1117">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e6cb-1118">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1118">See below.</span></span> | <span data-ttu-id="6e6cb-1119">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6e6cb-1120">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e6cb-1121">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1121">Option</span></span> | <span data-ttu-id="6e6cb-1122">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1122">Default Value</span></span> | <span data-ttu-id="6e6cb-1123">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e6cb-1124">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1124">90 seconds</span></span> | <span data-ttu-id="6e6cb-1125">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e6cb-1126">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e6cb-1127">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e6cb-1128">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1128">Option</span></span> | <span data-ttu-id="6e6cb-1129">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1129">Default Value</span></span> | <span data-ttu-id="6e6cb-1130">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e6cb-1131">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1131">5 seconds</span></span> | <span data-ttu-id="6e6cb-1132">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e6cb-1133">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e6cb-1134">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e6cb-1135">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1135">Configure client options</span></span>

<span data-ttu-id="6e6cb-1136">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e6cb-1137">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e6cb-1138">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1138">Configure logging</span></span>

<span data-ttu-id="6e6cb-1139">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e6cb-1140">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e6cb-1141">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e6cb-1142">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e6cb-1143">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e6cb-1144">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e6cb-1145">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e6cb-1146">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e6cb-1147">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e6cb-1148">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e6cb-1149">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e6cb-1150">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e6cb-1151">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e6cb-1152">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e6cb-1153">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e6cb-1154">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e6cb-1155">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e6cb-1156">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1156">Configure allowed transports</span></span>

<span data-ttu-id="6e6cb-1157">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e6cb-1158">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e6cb-1159">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="6e6cb-1160">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e6cb-1161">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e6cb-1162">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1162">Configure bearer authentication</span></span>

<span data-ttu-id="6e6cb-1163">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e6cb-1164">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e6cb-1165">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e6cb-1166">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e6cb-1167">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e6cb-1168">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e6cb-1169">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e6cb-1170">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e6cb-1171">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e6cb-1172">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e6cb-1173">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-1175">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1175">Option</span></span> | <span data-ttu-id="6e6cb-1176">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1176">Default value</span></span> | <span data-ttu-id="6e6cb-1177">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e6cb-1178">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-1179">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1179">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-1180">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-1181">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-1182">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e6cb-1183">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1183">15 seconds</span></span> | <span data-ttu-id="6e6cb-1184">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-1185">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e6cb-1186">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-1187">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="6e6cb-1188">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-1190">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1190">Option</span></span> | <span data-ttu-id="6e6cb-1191">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1191">Default value</span></span> | <span data-ttu-id="6e6cb-1192">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e6cb-1193">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-1194">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1194">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-1195">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e6cb-1196">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-1197">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-1198">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-1199">Opcja</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1199">Option</span></span> | <span data-ttu-id="6e6cb-1200">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1200">Default value</span></span> | <span data-ttu-id="6e6cb-1201">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e6cb-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e6cb-1203">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e6cb-1204">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1204">Timeout for server activity.</span></span> <span data-ttu-id="6e6cb-1205">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-1206">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e6cb-1207">Zalecana wartość to liczba z co najmniej podwójną `KeepAliveInterval` wartością serwera, aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e6cb-1208">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1208">15 seconds</span></span> | <span data-ttu-id="6e6cb-1209">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e6cb-1210">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e6cb-1211">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e6cb-1212">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e6cb-1213">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1213">Configure additional options</span></span>

<span data-ttu-id="6e6cb-1214">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e6cb-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e6cb-1216">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1216">.NET Option</span></span> |  <span data-ttu-id="6e6cb-1217">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1217">Default value</span></span> | <span data-ttu-id="6e6cb-1218">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-1219">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e6cb-1220">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-1221">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-1222">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e6cb-1223">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1223">Empty</span></span> | <span data-ttu-id="6e6cb-1224">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e6cb-1225">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1225">Empty</span></span> | <span data-ttu-id="6e6cb-1226">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e6cb-1227">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1227">Empty</span></span> | <span data-ttu-id="6e6cb-1228">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e6cb-1229">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1229">5 seconds</span></span> | <span data-ttu-id="6e6cb-1230">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1230">WebSockets only.</span></span> <span data-ttu-id="6e6cb-1231">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e6cb-1232">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e6cb-1233">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1233">Empty</span></span> | <span data-ttu-id="6e6cb-1234">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e6cb-1235">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e6cb-1236">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e6cb-1237">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e6cb-1238">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e6cb-1239">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e6cb-1240">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e6cb-1241">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e6cb-1242">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e6cb-1243">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e6cb-1244">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e6cb-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e6cb-1246">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1246">JavaScript Option</span></span> | <span data-ttu-id="6e6cb-1247">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1247">Default Value</span></span> | <span data-ttu-id="6e6cb-1248">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e6cb-1249">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="6e6cb-1250">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e6cb-1251">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-1252">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-1253">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e6cb-1254">Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="6e6cb-1255">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1255">Java Option</span></span> | <span data-ttu-id="6e6cb-1256">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1256">Default Value</span></span> | <span data-ttu-id="6e6cb-1257">Opis</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e6cb-1258">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e6cb-1259">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e6cb-1260">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e6cb-1261">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e6cb-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e6cb-1263">Pusty</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1263">Empty</span></span> | <span data-ttu-id="6e6cb-1264">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e6cb-1265">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e6cb-1266">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e6cb-1267">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e6cb-1268">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6e6cb-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
