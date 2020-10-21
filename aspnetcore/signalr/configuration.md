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
ms.openlocfilehash: 8851246dbaa076af1fdbc4e5e4f1ada0e4e3988a
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326593"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="27474-103">SignalRKonfiguracja ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="27474-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27474-104">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27474-105">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27474-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27474-106">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="27474-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27474-107">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="27474-108">`AddJsonProtocol`można dodać po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do programu `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27474-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="27474-109">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="27474-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27474-110">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27474-111">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="27474-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="27474-112">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27474-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="27474-113">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27474-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27474-114">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27474-115">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="27474-116">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="27474-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="27474-117">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="27474-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27474-118">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-118">MessagePack serialization options</span></span>

<span data-ttu-id="27474-119">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27474-120">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-121">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27474-122">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="27474-122">Configure server options</span></span>

<span data-ttu-id="27474-123">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="27474-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="27474-124">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-124">Option</span></span> | <span data-ttu-id="27474-125">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-125">Default Value</span></span> | <span data-ttu-id="27474-126">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="27474-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-127">30 seconds</span></span> | <span data-ttu-id="27474-128">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="27474-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="27474-129">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="27474-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="27474-130">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="27474-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="27474-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-131">15 seconds</span></span> | <span data-ttu-id="27474-132">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="27474-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27474-133">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-134">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27474-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-135">15 seconds</span></span> | <span data-ttu-id="27474-136">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27474-137">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="27474-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27474-138">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="27474-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27474-139">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="27474-139">All installed protocols</span></span> | <span data-ttu-id="27474-140">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-140">Protocols supported by this hub.</span></span> <span data-ttu-id="27474-141">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="27474-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27474-142">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27474-143">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="27474-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="27474-144">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="27474-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="27474-145">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="27474-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="27474-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-146">32 KB</span></span> | <span data-ttu-id="27474-147">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="27474-148">1</span><span class="sxs-lookup"><span data-stu-id="27474-148">1</span></span> | <span data-ttu-id="27474-149">Maksymalna liczba metod centralnych, które każdy klient może wywołać równolegle przed kolejkowanie.</span><span class="sxs-lookup"><span data-stu-id="27474-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="27474-150">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27474-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="27474-151">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27474-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27474-152">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="27474-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27474-153">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27474-154">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27474-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="27474-155">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="27474-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="27474-156">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-156">Option</span></span> | <span data-ttu-id="27474-157">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-157">Default Value</span></span> | <span data-ttu-id="27474-158">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27474-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-159">32 KB</span></span> | <span data-ttu-id="27474-160">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="27474-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="27474-161">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27474-162">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27474-163">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27474-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-164">32 KB</span></span> | <span data-ttu-id="27474-165">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="27474-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="27474-166">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27474-167">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-167">All Transports are enabled.</span></span> | <span data-ttu-id="27474-168">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27474-169">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-169">See below.</span></span> | <span data-ttu-id="27474-170">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27474-171">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-171">See below.</span></span> | <span data-ttu-id="27474-172">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="27474-173">0</span><span class="sxs-lookup"><span data-stu-id="27474-173">0</span></span> | <span data-ttu-id="27474-174">Określ minimalną wersję protokołu negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="27474-175">Służy do ograniczania liczby klientów do nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="27474-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="27474-176">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27474-177">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-177">Option</span></span> | <span data-ttu-id="27474-178">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-178">Default Value</span></span> | <span data-ttu-id="27474-179">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27474-180">90 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-180">90 seconds</span></span> | <span data-ttu-id="27474-181">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27474-182">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="27474-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27474-183">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27474-184">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-184">Option</span></span> | <span data-ttu-id="27474-185">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-185">Default Value</span></span> | <span data-ttu-id="27474-186">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27474-187">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-187">5 seconds</span></span> | <span data-ttu-id="27474-188">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="27474-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27474-189">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="27474-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27474-190">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27474-191">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="27474-191">Configure client options</span></span>

<span data-ttu-id="27474-192">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27474-193">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="27474-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27474-194">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="27474-194">Configure logging</span></span>

<span data-ttu-id="27474-195">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="27474-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27474-196">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="27474-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27474-197">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27474-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-198">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="27474-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27474-199">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="27474-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27474-200">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="27474-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27474-201">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27474-202">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="27474-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27474-203">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="27474-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27474-204">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="27474-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="27474-205">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="27474-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="27474-206">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="27474-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="27474-207">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="27474-207">The following table lists the available log levels.</span></span> <span data-ttu-id="27474-208">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="27474-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="27474-209">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="27474-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="27474-210">Ciąg</span><span class="sxs-lookup"><span data-stu-id="27474-210">String</span></span>                      | <span data-ttu-id="27474-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="27474-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="27474-212">`info` **lub** `information`</span><span class="sxs-lookup"><span data-stu-id="27474-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="27474-213">`warn` **lub** `warning`</span><span class="sxs-lookup"><span data-stu-id="27474-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="27474-214">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="27474-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27474-215">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27474-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27474-216">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27474-217">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27474-218">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="27474-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27474-219">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="27474-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27474-220">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="27474-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27474-221">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="27474-221">Configure allowed transports</span></span>

<span data-ttu-id="27474-222">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27474-223">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="27474-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27474-224">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-224">All transports are enabled by default.</span></span>

<span data-ttu-id="27474-225">Na przykład, aby wyłączyć transport zdarzeń Server-Sent, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="27474-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27474-226">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="27474-227">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="27474-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="27474-228">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="27474-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="27474-229">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27474-230">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="27474-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27474-231">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="27474-231">Configure bearer authentication</span></span>

<span data-ttu-id="27474-232">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="27474-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27474-233">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27474-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27474-234">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w konkretnym przypadku Server-Sent zdarzeń i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="27474-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27474-235">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27474-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27474-236">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27474-237">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27474-238">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27474-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27474-239">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27474-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27474-240">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27474-241">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="27474-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27474-242">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="27474-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-243">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-244">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-244">Option</span></span> | <span data-ttu-id="27474-245">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-245">Default value</span></span> | <span data-ttu-id="27474-246">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27474-247">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-248">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-248">Timeout for server activity.</span></span> <span data-ttu-id="27474-249">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-250">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-251">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27474-252">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-252">15 seconds</span></span> | <span data-ttu-id="27474-253">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-254">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-255">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-256">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27474-257">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-257">15 seconds</span></span> | <span data-ttu-id="27474-258">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-259">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-260">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="27474-261">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27474-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-263">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-263">Option</span></span> | <span data-ttu-id="27474-264">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-264">Default value</span></span> | <span data-ttu-id="27474-265">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27474-266">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-267">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-267">Timeout for server activity.</span></span> <span data-ttu-id="27474-268">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27474-269">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-270">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="27474-271">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27474-272">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-273">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-274">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-275">Java</span><span class="sxs-lookup"><span data-stu-id="27474-275">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-276">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-276">Option</span></span> | <span data-ttu-id="27474-277">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-277">Default value</span></span> | <span data-ttu-id="27474-278">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27474-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27474-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27474-280">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-281">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-281">Timeout for server activity.</span></span> <span data-ttu-id="27474-282">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-283">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-284">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27474-285">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-285">15 seconds</span></span> | <span data-ttu-id="27474-286">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-287">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-288">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-289">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="27474-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="27474-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="27474-291">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27474-292">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-293">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-294">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27474-295">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="27474-295">Configure additional options</span></span>

<span data-ttu-id="27474-296">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="27474-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-297">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-298">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="27474-298">.NET Option</span></span> |  <span data-ttu-id="27474-299">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-299">Default value</span></span> | <span data-ttu-id="27474-300">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27474-301">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27474-302">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-303">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-304">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27474-305">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-305">Empty</span></span> | <span data-ttu-id="27474-306">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="27474-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="27474-307">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-307">Empty</span></span> | <span data-ttu-id="27474-308">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="27474-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27474-309">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-309">Empty</span></span> | <span data-ttu-id="27474-310">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27474-311">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-311">5 seconds</span></span> | <span data-ttu-id="27474-312">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-312">WebSockets only.</span></span> <span data-ttu-id="27474-313">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="27474-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27474-314">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27474-315">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-315">Empty</span></span> | <span data-ttu-id="27474-316">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27474-317">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27474-318">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="27474-319">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="27474-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27474-320">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="27474-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27474-321">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="27474-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27474-322">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27474-323">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27474-324">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="27474-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27474-325">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27474-326">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="27474-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27474-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-328">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-328">JavaScript Option</span></span> | <span data-ttu-id="27474-329">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-329">Default Value</span></span> | <span data-ttu-id="27474-330">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27474-331">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27474-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Wartość określająca transport, który ma być używany w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="27474-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="27474-333">Słownik nagłówków wysłanych z każdym żądaniem HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="27474-334">Wysyłanie nagłówków w przeglądarce nie działa w przypadku obiektów WebSockets ani <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> strumienia.</span><span class="sxs-lookup"><span data-stu-id="27474-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27474-335">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27474-336">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-337">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-338">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="27474-339">Określa, czy poświadczenia będą wysyłane z żądaniem CORS.</span><span class="sxs-lookup"><span data-stu-id="27474-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="27474-340">Azure App Service używa cookie s dla sesji programu Sticky i wymaga, aby ta opcja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="27474-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="27474-341">Aby uzyskać więcej informacji na temat mechanizmu CORS w programie SignalR , zobacz <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="27474-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-342">Java</span><span class="sxs-lookup"><span data-stu-id="27474-342">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-343">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="27474-343">Java Option</span></span> | <span data-ttu-id="27474-344">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-344">Default Value</span></span> | <span data-ttu-id="27474-345">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27474-346">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27474-347">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-348">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-349">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="27474-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27474-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27474-351">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-351">Empty</span></span> | <span data-ttu-id="27474-352">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27474-353">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27474-354">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="27474-355">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27474-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27474-356">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="27474-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27474-357">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27474-358">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27474-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27474-359">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="27474-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27474-360">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="27474-361">`AddJsonProtocol`można dodać po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do programu `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27474-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="27474-362">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="27474-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27474-363">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27474-364">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="27474-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="27474-365">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27474-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="27474-366">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27474-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27474-367">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27474-368">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="27474-369">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="27474-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="27474-370">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="27474-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27474-371">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-371">MessagePack serialization options</span></span>

<span data-ttu-id="27474-372">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27474-373">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-374">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27474-375">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="27474-375">Configure server options</span></span>

<span data-ttu-id="27474-376">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="27474-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="27474-377">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-377">Option</span></span> | <span data-ttu-id="27474-378">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-378">Default Value</span></span> | <span data-ttu-id="27474-379">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="27474-380">30 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-380">30 seconds</span></span> | <span data-ttu-id="27474-381">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="27474-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="27474-382">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="27474-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="27474-383">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="27474-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="27474-384">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-384">15 seconds</span></span> | <span data-ttu-id="27474-385">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="27474-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27474-386">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-387">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27474-388">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-388">15 seconds</span></span> | <span data-ttu-id="27474-389">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27474-390">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="27474-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27474-391">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="27474-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27474-392">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="27474-392">All installed protocols</span></span> | <span data-ttu-id="27474-393">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-393">Protocols supported by this hub.</span></span> <span data-ttu-id="27474-394">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="27474-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27474-395">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27474-396">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="27474-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="27474-397">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="27474-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="27474-398">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="27474-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="27474-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-399">32 KB</span></span> | <span data-ttu-id="27474-400">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="27474-401">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27474-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="27474-402">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27474-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27474-403">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="27474-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27474-404">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27474-405">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27474-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="27474-406">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="27474-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="27474-407">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-407">Option</span></span> | <span data-ttu-id="27474-408">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-408">Default Value</span></span> | <span data-ttu-id="27474-409">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27474-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-410">32 KB</span></span> | <span data-ttu-id="27474-411">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="27474-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="27474-412">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27474-413">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27474-414">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27474-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-415">32 KB</span></span> | <span data-ttu-id="27474-416">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="27474-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="27474-417">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27474-418">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-418">All Transports are enabled.</span></span> | <span data-ttu-id="27474-419">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27474-420">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-420">See below.</span></span> | <span data-ttu-id="27474-421">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27474-422">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-422">See below.</span></span> | <span data-ttu-id="27474-423">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="27474-424">0</span><span class="sxs-lookup"><span data-stu-id="27474-424">0</span></span> | <span data-ttu-id="27474-425">Określ minimalną wersję protokołu negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="27474-426">Służy do ograniczania liczby klientów do nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="27474-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="27474-427">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27474-428">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-428">Option</span></span> | <span data-ttu-id="27474-429">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-429">Default Value</span></span> | <span data-ttu-id="27474-430">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27474-431">90 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-431">90 seconds</span></span> | <span data-ttu-id="27474-432">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27474-433">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="27474-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27474-434">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27474-435">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-435">Option</span></span> | <span data-ttu-id="27474-436">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-436">Default Value</span></span> | <span data-ttu-id="27474-437">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27474-438">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-438">5 seconds</span></span> | <span data-ttu-id="27474-439">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="27474-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27474-440">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="27474-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27474-441">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27474-442">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="27474-442">Configure client options</span></span>

<span data-ttu-id="27474-443">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27474-444">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="27474-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27474-445">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="27474-445">Configure logging</span></span>

<span data-ttu-id="27474-446">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="27474-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27474-447">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="27474-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27474-448">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27474-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-449">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="27474-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27474-450">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="27474-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27474-451">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="27474-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27474-452">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27474-453">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="27474-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27474-454">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="27474-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27474-455">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="27474-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="27474-456">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="27474-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="27474-457">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="27474-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="27474-458">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="27474-458">The following table lists the available log levels.</span></span> <span data-ttu-id="27474-459">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="27474-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="27474-460">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="27474-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="27474-461">Ciąg</span><span class="sxs-lookup"><span data-stu-id="27474-461">String</span></span>                      | <span data-ttu-id="27474-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="27474-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="27474-463">`info` **lub** `information`</span><span class="sxs-lookup"><span data-stu-id="27474-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="27474-464">`warn` **lub** `warning`</span><span class="sxs-lookup"><span data-stu-id="27474-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="27474-465">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="27474-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27474-466">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27474-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27474-467">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27474-468">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27474-469">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="27474-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27474-470">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="27474-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27474-471">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="27474-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27474-472">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="27474-472">Configure allowed transports</span></span>

<span data-ttu-id="27474-473">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27474-474">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="27474-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27474-475">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-475">All transports are enabled by default.</span></span>

<span data-ttu-id="27474-476">Na przykład, aby wyłączyć transport zdarzeń Server-Sent, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="27474-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27474-477">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="27474-478">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="27474-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="27474-479">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="27474-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="27474-480">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27474-481">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="27474-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27474-482">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="27474-482">Configure bearer authentication</span></span>

<span data-ttu-id="27474-483">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="27474-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27474-484">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27474-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27474-485">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w konkretnym przypadku Server-Sent zdarzeń i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="27474-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27474-486">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27474-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27474-487">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27474-488">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27474-489">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27474-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27474-490">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27474-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27474-491">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27474-492">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="27474-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27474-493">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="27474-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-494">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-495">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-495">Option</span></span> | <span data-ttu-id="27474-496">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-496">Default value</span></span> | <span data-ttu-id="27474-497">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27474-498">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-499">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-499">Timeout for server activity.</span></span> <span data-ttu-id="27474-500">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-501">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-502">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27474-503">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-503">15 seconds</span></span> | <span data-ttu-id="27474-504">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-505">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-506">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-507">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27474-508">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-508">15 seconds</span></span> | <span data-ttu-id="27474-509">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-510">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-511">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="27474-512">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27474-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-514">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-514">Option</span></span> | <span data-ttu-id="27474-515">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-515">Default value</span></span> | <span data-ttu-id="27474-516">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27474-517">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-518">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-518">Timeout for server activity.</span></span> <span data-ttu-id="27474-519">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27474-520">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-521">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="27474-522">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27474-523">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-524">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-525">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-526">Java</span><span class="sxs-lookup"><span data-stu-id="27474-526">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-527">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-527">Option</span></span> | <span data-ttu-id="27474-528">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-528">Default value</span></span> | <span data-ttu-id="27474-529">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27474-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27474-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27474-531">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-532">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-532">Timeout for server activity.</span></span> <span data-ttu-id="27474-533">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-534">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-535">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27474-536">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-536">15 seconds</span></span> | <span data-ttu-id="27474-537">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-538">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-539">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-540">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="27474-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="27474-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="27474-542">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27474-543">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-544">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-545">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27474-546">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="27474-546">Configure additional options</span></span>

<span data-ttu-id="27474-547">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="27474-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-548">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-549">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="27474-549">.NET Option</span></span> |  <span data-ttu-id="27474-550">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-550">Default value</span></span> | <span data-ttu-id="27474-551">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27474-552">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27474-553">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-554">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-555">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27474-556">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-556">Empty</span></span> | <span data-ttu-id="27474-557">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="27474-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="27474-558">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-558">Empty</span></span> | <span data-ttu-id="27474-559">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="27474-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27474-560">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-560">Empty</span></span> | <span data-ttu-id="27474-561">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27474-562">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-562">5 seconds</span></span> | <span data-ttu-id="27474-563">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-563">WebSockets only.</span></span> <span data-ttu-id="27474-564">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="27474-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27474-565">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27474-566">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-566">Empty</span></span> | <span data-ttu-id="27474-567">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27474-568">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27474-569">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="27474-570">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="27474-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27474-571">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="27474-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27474-572">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="27474-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27474-573">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27474-574">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27474-575">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="27474-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27474-576">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27474-577">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="27474-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27474-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-579">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-579">JavaScript Option</span></span> | <span data-ttu-id="27474-580">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-580">Default Value</span></span> | <span data-ttu-id="27474-581">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27474-582">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27474-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Wartość określająca transport, który ma być używany w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="27474-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27474-584">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27474-585">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-586">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-587">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-588">Java</span><span class="sxs-lookup"><span data-stu-id="27474-588">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-589">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="27474-589">Java Option</span></span> | <span data-ttu-id="27474-590">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-590">Default Value</span></span> | <span data-ttu-id="27474-591">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27474-592">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27474-593">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-594">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-595">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="27474-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27474-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27474-597">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-597">Empty</span></span> | <span data-ttu-id="27474-598">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27474-599">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27474-600">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="27474-601">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27474-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27474-602">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="27474-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27474-603">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27474-604">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27474-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27474-605">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="27474-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27474-606">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="27474-607">`AddJsonProtocol`można dodać po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do programu `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27474-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="27474-608">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="27474-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27474-609">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27474-610">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="27474-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="27474-611">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27474-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="27474-612">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27474-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27474-613">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27474-614">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="27474-615">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="27474-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="27474-616">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="27474-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27474-617">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-617">MessagePack serialization options</span></span>

<span data-ttu-id="27474-618">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27474-619">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-620">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27474-621">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="27474-621">Configure server options</span></span>

<span data-ttu-id="27474-622">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="27474-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="27474-623">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-623">Option</span></span> | <span data-ttu-id="27474-624">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-624">Default Value</span></span> | <span data-ttu-id="27474-625">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="27474-626">30 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-626">30 seconds</span></span> | <span data-ttu-id="27474-627">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="27474-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="27474-628">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="27474-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="27474-629">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="27474-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="27474-630">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-630">15 seconds</span></span> | <span data-ttu-id="27474-631">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="27474-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27474-632">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-633">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27474-634">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-634">15 seconds</span></span> | <span data-ttu-id="27474-635">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27474-636">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="27474-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27474-637">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="27474-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27474-638">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="27474-638">All installed protocols</span></span> | <span data-ttu-id="27474-639">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-639">Protocols supported by this hub.</span></span> <span data-ttu-id="27474-640">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="27474-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27474-641">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27474-642">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="27474-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="27474-643">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="27474-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="27474-644">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="27474-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="27474-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-645">32 KB</span></span> | <span data-ttu-id="27474-646">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="27474-647">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27474-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="27474-648">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27474-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27474-649">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="27474-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27474-650">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27474-651">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27474-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="27474-652">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="27474-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="27474-653">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-653">Option</span></span> | <span data-ttu-id="27474-654">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-654">Default Value</span></span> | <span data-ttu-id="27474-655">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27474-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-656">32 KB</span></span> | <span data-ttu-id="27474-657">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="27474-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="27474-658">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27474-659">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27474-660">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27474-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-661">32 KB</span></span> | <span data-ttu-id="27474-662">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="27474-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="27474-663">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27474-664">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-664">All Transports are enabled.</span></span> | <span data-ttu-id="27474-665">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27474-666">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-666">See below.</span></span> | <span data-ttu-id="27474-667">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27474-668">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-668">See below.</span></span> | <span data-ttu-id="27474-669">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="27474-670">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27474-671">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-671">Option</span></span> | <span data-ttu-id="27474-672">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-672">Default Value</span></span> | <span data-ttu-id="27474-673">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27474-674">90 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-674">90 seconds</span></span> | <span data-ttu-id="27474-675">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27474-676">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="27474-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27474-677">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27474-678">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-678">Option</span></span> | <span data-ttu-id="27474-679">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-679">Default Value</span></span> | <span data-ttu-id="27474-680">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27474-681">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-681">5 seconds</span></span> | <span data-ttu-id="27474-682">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="27474-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27474-683">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="27474-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27474-684">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27474-685">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="27474-685">Configure client options</span></span>

<span data-ttu-id="27474-686">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27474-687">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="27474-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27474-688">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="27474-688">Configure logging</span></span>

<span data-ttu-id="27474-689">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="27474-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27474-690">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="27474-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27474-691">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27474-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-692">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="27474-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27474-693">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="27474-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27474-694">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="27474-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27474-695">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27474-696">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="27474-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27474-697">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="27474-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27474-698">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="27474-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="27474-699">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="27474-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="27474-700">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="27474-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="27474-701">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="27474-701">The following table lists the available log levels.</span></span> <span data-ttu-id="27474-702">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="27474-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="27474-703">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="27474-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="27474-704">Ciąg</span><span class="sxs-lookup"><span data-stu-id="27474-704">String</span></span>                      | <span data-ttu-id="27474-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="27474-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="27474-706">`info` **lub** `information`</span><span class="sxs-lookup"><span data-stu-id="27474-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="27474-707">`warn` **lub** `warning`</span><span class="sxs-lookup"><span data-stu-id="27474-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="27474-708">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="27474-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27474-709">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27474-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27474-710">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27474-711">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27474-712">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="27474-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27474-713">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="27474-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27474-714">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="27474-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27474-715">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="27474-715">Configure allowed transports</span></span>

<span data-ttu-id="27474-716">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27474-717">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="27474-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27474-718">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-718">All transports are enabled by default.</span></span>

<span data-ttu-id="27474-719">Na przykład, aby wyłączyć transport zdarzeń Server-Sent, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="27474-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27474-720">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="27474-721">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="27474-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="27474-722">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="27474-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="27474-723">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27474-724">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="27474-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27474-725">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="27474-725">Configure bearer authentication</span></span>

<span data-ttu-id="27474-726">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="27474-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27474-727">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27474-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27474-728">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w konkretnym przypadku Server-Sent zdarzeń i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="27474-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27474-729">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27474-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27474-730">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27474-731">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27474-732">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27474-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27474-733">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27474-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27474-734">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27474-735">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="27474-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27474-736">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="27474-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-737">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-738">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-738">Option</span></span> | <span data-ttu-id="27474-739">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-739">Default value</span></span> | <span data-ttu-id="27474-740">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27474-741">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-742">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-742">Timeout for server activity.</span></span> <span data-ttu-id="27474-743">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-744">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-745">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27474-746">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-746">15 seconds</span></span> | <span data-ttu-id="27474-747">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-748">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-749">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-750">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27474-751">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-751">15 seconds</span></span> | <span data-ttu-id="27474-752">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-753">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-754">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="27474-755">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27474-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-757">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-757">Option</span></span> | <span data-ttu-id="27474-758">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-758">Default value</span></span> | <span data-ttu-id="27474-759">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27474-760">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-761">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-761">Timeout for server activity.</span></span> <span data-ttu-id="27474-762">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27474-763">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-764">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="27474-765">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27474-766">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-767">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-768">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-769">Java</span><span class="sxs-lookup"><span data-stu-id="27474-769">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-770">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-770">Option</span></span> | <span data-ttu-id="27474-771">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-771">Default value</span></span> | <span data-ttu-id="27474-772">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27474-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27474-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27474-774">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-775">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-775">Timeout for server activity.</span></span> <span data-ttu-id="27474-776">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-777">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-778">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27474-779">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-779">15 seconds</span></span> | <span data-ttu-id="27474-780">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-781">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-782">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-783">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="27474-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="27474-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="27474-785">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27474-786">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-787">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-788">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27474-789">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="27474-789">Configure additional options</span></span>

<span data-ttu-id="27474-790">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="27474-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-791">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-792">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="27474-792">.NET Option</span></span> |  <span data-ttu-id="27474-793">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-793">Default value</span></span> | <span data-ttu-id="27474-794">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27474-795">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27474-796">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-797">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-798">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27474-799">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-799">Empty</span></span> | <span data-ttu-id="27474-800">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="27474-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="27474-801">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-801">Empty</span></span> | <span data-ttu-id="27474-802">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="27474-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27474-803">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-803">Empty</span></span> | <span data-ttu-id="27474-804">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27474-805">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-805">5 seconds</span></span> | <span data-ttu-id="27474-806">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-806">WebSockets only.</span></span> <span data-ttu-id="27474-807">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="27474-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27474-808">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27474-809">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-809">Empty</span></span> | <span data-ttu-id="27474-810">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27474-811">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27474-812">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="27474-813">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="27474-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27474-814">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="27474-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27474-815">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="27474-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27474-816">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27474-817">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27474-818">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="27474-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27474-819">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27474-820">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="27474-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27474-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-822">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-822">JavaScript Option</span></span> | <span data-ttu-id="27474-823">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-823">Default Value</span></span> | <span data-ttu-id="27474-824">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27474-825">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27474-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Wartość określająca transport, który ma być używany w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="27474-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27474-827">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27474-828">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-829">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-830">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-831">Java</span><span class="sxs-lookup"><span data-stu-id="27474-831">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-832">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="27474-832">Java Option</span></span> | <span data-ttu-id="27474-833">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-833">Default Value</span></span> | <span data-ttu-id="27474-834">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27474-835">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27474-836">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-837">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-838">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="27474-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27474-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27474-840">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-840">Empty</span></span> | <span data-ttu-id="27474-841">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27474-842">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27474-843">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="27474-844">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27474-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27474-845">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="27474-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27474-846">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27474-847">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27474-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27474-848">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="27474-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27474-849">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może być dodana po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="27474-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="27474-850">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="27474-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27474-851">Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27474-852">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="27474-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="27474-853">Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27474-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="27474-854">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27474-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27474-855">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27474-856">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27474-857">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-857">MessagePack serialization options</span></span>

<span data-ttu-id="27474-858">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27474-859">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-860">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27474-861">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="27474-861">Configure server options</span></span>

<span data-ttu-id="27474-862">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="27474-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="27474-863">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-863">Option</span></span> | <span data-ttu-id="27474-864">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-864">Default Value</span></span> | <span data-ttu-id="27474-865">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="27474-866">30 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-866">30 seconds</span></span> | <span data-ttu-id="27474-867">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="27474-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="27474-868">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="27474-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="27474-869">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="27474-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="27474-870">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-870">15 seconds</span></span> | <span data-ttu-id="27474-871">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="27474-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27474-872">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-873">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27474-874">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-874">15 seconds</span></span> | <span data-ttu-id="27474-875">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27474-876">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="27474-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27474-877">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="27474-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27474-878">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="27474-878">All installed protocols</span></span> | <span data-ttu-id="27474-879">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-879">Protocols supported by this hub.</span></span> <span data-ttu-id="27474-880">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="27474-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27474-881">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27474-882">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="27474-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="27474-883">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27474-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="27474-884">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27474-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27474-885">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="27474-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27474-886">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27474-887">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27474-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="27474-888">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="27474-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="27474-889">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-889">Option</span></span> | <span data-ttu-id="27474-890">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-890">Default Value</span></span> | <span data-ttu-id="27474-891">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27474-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-892">32 KB</span></span> | <span data-ttu-id="27474-893">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer.</span><span class="sxs-lookup"><span data-stu-id="27474-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="27474-894">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27474-895">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27474-896">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27474-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-897">32 KB</span></span> | <span data-ttu-id="27474-898">Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer.</span><span class="sxs-lookup"><span data-stu-id="27474-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="27474-899">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27474-900">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-900">All Transports are enabled.</span></span> | <span data-ttu-id="27474-901">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27474-902">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-902">See below.</span></span> | <span data-ttu-id="27474-903">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27474-904">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-904">See below.</span></span> | <span data-ttu-id="27474-905">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="27474-906">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27474-907">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-907">Option</span></span> | <span data-ttu-id="27474-908">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-908">Default Value</span></span> | <span data-ttu-id="27474-909">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27474-910">90 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-910">90 seconds</span></span> | <span data-ttu-id="27474-911">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27474-912">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="27474-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27474-913">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27474-914">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-914">Option</span></span> | <span data-ttu-id="27474-915">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-915">Default Value</span></span> | <span data-ttu-id="27474-916">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27474-917">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-917">5 seconds</span></span> | <span data-ttu-id="27474-918">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="27474-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27474-919">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="27474-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27474-920">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27474-921">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="27474-921">Configure client options</span></span>

<span data-ttu-id="27474-922">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27474-923">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="27474-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27474-924">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="27474-924">Configure logging</span></span>

<span data-ttu-id="27474-925">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="27474-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27474-926">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="27474-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27474-927">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27474-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-928">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="27474-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27474-929">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="27474-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27474-930">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="27474-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27474-931">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27474-932">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="27474-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27474-933">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="27474-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27474-934">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="27474-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27474-935">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="27474-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27474-936">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27474-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27474-937">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27474-938">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27474-939">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="27474-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27474-940">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="27474-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27474-941">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="27474-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27474-942">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="27474-942">Configure allowed transports</span></span>

<span data-ttu-id="27474-943">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27474-944">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="27474-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27474-945">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-945">All transports are enabled by default.</span></span>

<span data-ttu-id="27474-946">Na przykład, aby wyłączyć transport zdarzeń Server-Sent, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="27474-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27474-947">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="27474-948">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="27474-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27474-949">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="27474-949">Configure bearer authentication</span></span>

<span data-ttu-id="27474-950">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="27474-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27474-951">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27474-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27474-952">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w konkretnym przypadku Server-Sent zdarzeń i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="27474-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27474-953">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27474-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27474-954">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27474-955">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27474-956">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27474-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27474-957">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27474-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27474-958">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27474-959">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="27474-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27474-960">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="27474-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-961">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-962">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-962">Option</span></span> | <span data-ttu-id="27474-963">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-963">Default value</span></span> | <span data-ttu-id="27474-964">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27474-965">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-966">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-966">Timeout for server activity.</span></span> <span data-ttu-id="27474-967">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-968">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-969">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27474-970">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-970">15 seconds</span></span> | <span data-ttu-id="27474-971">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-972">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-973">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-974">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27474-975">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-975">15 seconds</span></span> | <span data-ttu-id="27474-976">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-977">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-978">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="27474-979">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27474-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-981">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-981">Option</span></span> | <span data-ttu-id="27474-982">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-982">Default value</span></span> | <span data-ttu-id="27474-983">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27474-984">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-985">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-985">Timeout for server activity.</span></span> <span data-ttu-id="27474-986">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27474-987">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-988">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="27474-989">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27474-990">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-991">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-992">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-993">Java</span><span class="sxs-lookup"><span data-stu-id="27474-993">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-994">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-994">Option</span></span> | <span data-ttu-id="27474-995">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-995">Default value</span></span> | <span data-ttu-id="27474-996">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27474-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27474-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27474-998">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-999">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-999">Timeout for server activity.</span></span> <span data-ttu-id="27474-1000">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-1001">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-1002">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27474-1003">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-1003">15 seconds</span></span> | <span data-ttu-id="27474-1004">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-1005">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-1006">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-1007">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="27474-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="27474-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="27474-1009">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27474-1010">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27474-1011">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="27474-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27474-1012">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27474-1013">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="27474-1013">Configure additional options</span></span>

<span data-ttu-id="27474-1014">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="27474-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-1016">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="27474-1016">.NET Option</span></span> |  <span data-ttu-id="27474-1017">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1017">Default value</span></span> | <span data-ttu-id="27474-1018">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27474-1019">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27474-1020">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-1021">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-1022">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27474-1023">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1023">Empty</span></span> | <span data-ttu-id="27474-1024">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="27474-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="27474-1025">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1025">Empty</span></span> | <span data-ttu-id="27474-1026">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="27474-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27474-1027">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1027">Empty</span></span> | <span data-ttu-id="27474-1028">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27474-1029">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-1029">5 seconds</span></span> | <span data-ttu-id="27474-1030">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-1030">WebSockets only.</span></span> <span data-ttu-id="27474-1031">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="27474-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27474-1032">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27474-1033">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1033">Empty</span></span> | <span data-ttu-id="27474-1034">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27474-1035">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27474-1036">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="27474-1037">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="27474-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27474-1038">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="27474-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27474-1039">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="27474-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27474-1040">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27474-1041">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27474-1042">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="27474-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27474-1043">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27474-1044">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="27474-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27474-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-1046">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-1046">JavaScript Option</span></span> | <span data-ttu-id="27474-1047">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1047">Default Value</span></span> | <span data-ttu-id="27474-1048">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27474-1049">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27474-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Wartość określająca transport, który ma być używany w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="27474-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27474-1051">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27474-1052">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-1053">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-1054">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-1055">Java</span><span class="sxs-lookup"><span data-stu-id="27474-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-1056">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="27474-1056">Java Option</span></span> | <span data-ttu-id="27474-1057">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1057">Default Value</span></span> | <span data-ttu-id="27474-1058">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27474-1059">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27474-1060">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-1061">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-1062">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="27474-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27474-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27474-1064">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1064">Empty</span></span> | <span data-ttu-id="27474-1065">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27474-1066">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27474-1067">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="27474-1068">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27474-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27474-1069">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="27474-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27474-1070">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27474-1071">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27474-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27474-1072">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="27474-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27474-1073">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może być dodana po [dodaniu SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="27474-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="27474-1074">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="27474-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27474-1075">Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27474-1076">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="27474-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="27474-1077">Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27474-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="27474-1078">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27474-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27474-1079">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27474-1080">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27474-1081">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="27474-1081">MessagePack serialization options</span></span>

<span data-ttu-id="27474-1082">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27474-1083">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27474-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-1084">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27474-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27474-1085">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="27474-1085">Configure server options</span></span>

<span data-ttu-id="27474-1086">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="27474-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="27474-1087">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-1087">Option</span></span> | <span data-ttu-id="27474-1088">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1088">Default Value</span></span> | <span data-ttu-id="27474-1089">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="27474-1090">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-1090">15 seconds</span></span> | <span data-ttu-id="27474-1091">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="27474-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27474-1092">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-1093">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27474-1094">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-1094">15 seconds</span></span> | <span data-ttu-id="27474-1095">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27474-1096">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="27474-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27474-1097">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="27474-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27474-1098">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="27474-1098">All installed protocols</span></span> | <span data-ttu-id="27474-1099">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="27474-1100">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="27474-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27474-1101">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27474-1102">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="27474-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="27474-1103">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27474-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="27474-1104">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27474-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27474-1105">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="27474-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27474-1106">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27474-1107">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27474-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="27474-1108">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="27474-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="27474-1109">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-1109">Option</span></span> | <span data-ttu-id="27474-1110">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1110">Default Value</span></span> | <span data-ttu-id="27474-1111">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27474-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-1112">32 KB</span></span> | <span data-ttu-id="27474-1113">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer.</span><span class="sxs-lookup"><span data-stu-id="27474-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="27474-1114">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27474-1115">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27474-1116">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="27474-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27474-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="27474-1117">32 KB</span></span> | <span data-ttu-id="27474-1118">Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer.</span><span class="sxs-lookup"><span data-stu-id="27474-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="27474-1119">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="27474-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27474-1120">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-1120">All Transports are enabled.</span></span> | <span data-ttu-id="27474-1121">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="27474-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27474-1122">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-1122">See below.</span></span> | <span data-ttu-id="27474-1123">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27474-1124">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="27474-1124">See below.</span></span> | <span data-ttu-id="27474-1125">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="27474-1126">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27474-1127">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-1127">Option</span></span> | <span data-ttu-id="27474-1128">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1128">Default Value</span></span> | <span data-ttu-id="27474-1129">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27474-1130">90 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-1130">90 seconds</span></span> | <span data-ttu-id="27474-1131">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27474-1132">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="27474-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27474-1133">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="27474-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27474-1134">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-1134">Option</span></span> | <span data-ttu-id="27474-1135">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1135">Default Value</span></span> | <span data-ttu-id="27474-1136">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27474-1137">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-1137">5 seconds</span></span> | <span data-ttu-id="27474-1138">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="27474-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27474-1139">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="27474-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27474-1140">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27474-1141">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="27474-1141">Configure client options</span></span>

<span data-ttu-id="27474-1142">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27474-1143">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="27474-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27474-1144">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="27474-1144">Configure logging</span></span>

<span data-ttu-id="27474-1145">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="27474-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27474-1146">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="27474-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27474-1147">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27474-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27474-1148">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="27474-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27474-1149">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="27474-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27474-1150">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="27474-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27474-1151">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="27474-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27474-1152">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="27474-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27474-1153">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27474-1154">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="27474-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27474-1155">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="27474-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27474-1156">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27474-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27474-1157">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27474-1158">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27474-1159">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="27474-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27474-1160">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="27474-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27474-1161">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="27474-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27474-1162">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="27474-1162">Configure allowed transports</span></span>

<span data-ttu-id="27474-1163">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27474-1164">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="27474-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27474-1165">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="27474-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="27474-1166">Na przykład, aby wyłączyć transport zdarzeń Server-Sent, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="27474-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27474-1167">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27474-1168">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="27474-1168">Configure bearer authentication</span></span>

<span data-ttu-id="27474-1169">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="27474-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27474-1170">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27474-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27474-1171">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w konkretnym przypadku Server-Sent zdarzeń i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="27474-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27474-1172">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27474-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27474-1173">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27474-1174">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27474-1175">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27474-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27474-1176">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27474-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27474-1177">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27474-1178">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="27474-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27474-1179">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="27474-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-1181">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-1181">Option</span></span> | <span data-ttu-id="27474-1182">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1182">Default value</span></span> | <span data-ttu-id="27474-1183">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27474-1184">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-1185">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-1185">Timeout for server activity.</span></span> <span data-ttu-id="27474-1186">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-1187">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-1188">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27474-1189">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-1189">15 seconds</span></span> | <span data-ttu-id="27474-1190">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-1191">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27474-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27474-1192">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-1193">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="27474-1194">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="27474-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27474-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-1196">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-1196">Option</span></span> | <span data-ttu-id="27474-1197">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1197">Default value</span></span> | <span data-ttu-id="27474-1198">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27474-1199">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-1200">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-1200">Timeout for server activity.</span></span> <span data-ttu-id="27474-1201">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27474-1202">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-1203">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-1204">Java</span><span class="sxs-lookup"><span data-stu-id="27474-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-1205">Opcja</span><span class="sxs-lookup"><span data-stu-id="27474-1205">Option</span></span> | <span data-ttu-id="27474-1206">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1206">Default value</span></span> | <span data-ttu-id="27474-1207">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27474-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27474-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27474-1209">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="27474-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27474-1210">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="27474-1210">Timeout for server activity.</span></span> <span data-ttu-id="27474-1211">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-1212">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="27474-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27474-1213">Zalecana wartość to liczba z co najmniej podwójną `KeepAliveInterval` wartością serwera, aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="27474-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27474-1214">15 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-1214">15 seconds</span></span> | <span data-ttu-id="27474-1215">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="27474-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="27474-1216">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="27474-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27474-1217">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="27474-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27474-1218">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27474-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27474-1219">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="27474-1219">Configure additional options</span></span>

<span data-ttu-id="27474-1220">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="27474-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27474-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="27474-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27474-1222">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="27474-1222">.NET Option</span></span> |  <span data-ttu-id="27474-1223">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1223">Default value</span></span> | <span data-ttu-id="27474-1224">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27474-1225">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27474-1226">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-1227">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-1228">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27474-1229">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1229">Empty</span></span> | <span data-ttu-id="27474-1230">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="27474-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="27474-1231">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1231">Empty</span></span> | <span data-ttu-id="27474-1232">Kolekcja HTTP cookie s do wysłania w każdym ŻĄDANIU http.</span><span class="sxs-lookup"><span data-stu-id="27474-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27474-1233">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1233">Empty</span></span> | <span data-ttu-id="27474-1234">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27474-1235">5 sekund</span><span class="sxs-lookup"><span data-stu-id="27474-1235">5 seconds</span></span> | <span data-ttu-id="27474-1236">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-1236">WebSockets only.</span></span> <span data-ttu-id="27474-1237">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="27474-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27474-1238">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="27474-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27474-1239">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1239">Empty</span></span> | <span data-ttu-id="27474-1240">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27474-1241">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27474-1242">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="27474-1243">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="27474-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27474-1244">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="27474-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27474-1245">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak Cookie s i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="27474-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27474-1246">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27474-1247">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27474-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27474-1248">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="27474-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27474-1249">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27474-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27474-1250">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="27474-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27474-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27474-1252">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="27474-1252">JavaScript Option</span></span> | <span data-ttu-id="27474-1253">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1253">Default Value</span></span> | <span data-ttu-id="27474-1254">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27474-1255">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27474-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Wartość określająca transport, który ma być używany w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="27474-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27474-1257">Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="27474-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27474-1258">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-1259">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-1260">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27474-1261">Java</span><span class="sxs-lookup"><span data-stu-id="27474-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="27474-1262">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="27474-1262">Java Option</span></span> | <span data-ttu-id="27474-1263">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="27474-1263">Default Value</span></span> | <span data-ttu-id="27474-1264">Opis</span><span class="sxs-lookup"><span data-stu-id="27474-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27474-1265">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27474-1266">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="27474-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27474-1267">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="27474-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="27474-1268">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="27474-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="27474-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27474-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27474-1270">Pusty</span><span class="sxs-lookup"><span data-stu-id="27474-1270">Empty</span></span> | <span data-ttu-id="27474-1271">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="27474-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27474-1272">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27474-1273">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27474-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="27474-1274">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27474-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27474-1275">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="27474-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
