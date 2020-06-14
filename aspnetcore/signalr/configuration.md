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
ms.openlocfilehash: 09866f1fd56a4d0747ef3814c85ab5070cfb8d59
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756122"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="cb015-103">SignalRKonfiguracja ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb015-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb015-104">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb015-105">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb015-106">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="cb015-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb015-107">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="cb015-108">`AddJsonProtocol`można dodać po elemencie [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb015-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cb015-109">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="cb015-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb015-110">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb015-111">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="cb015-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="cb015-112">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb015-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="cb015-113">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="cb015-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb015-114">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cb015-115">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="cb015-116">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="cb015-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="cb015-117">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="cb015-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb015-118">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-118">MessagePack serialization options</span></span>

<span data-ttu-id="cb015-119">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb015-120">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-121">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb015-122">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="cb015-122">Configure server options</span></span>

<span data-ttu-id="cb015-123">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="cb015-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb015-124">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-124">Option</span></span> | <span data-ttu-id="cb015-125">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-125">Default Value</span></span> | <span data-ttu-id="cb015-126">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cb015-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-127">30 seconds</span></span> | <span data-ttu-id="cb015-128">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="cb015-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cb015-129">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="cb015-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cb015-130">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="cb015-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cb015-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-131">15 seconds</span></span> | <span data-ttu-id="cb015-132">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="cb015-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb015-133">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-134">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb015-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-135">15 seconds</span></span> | <span data-ttu-id="cb015-136">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb015-137">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="cb015-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb015-138">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="cb015-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb015-139">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="cb015-139">All installed protocols</span></span> | <span data-ttu-id="cb015-140">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-140">Protocols supported by this hub.</span></span> <span data-ttu-id="cb015-141">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="cb015-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb015-142">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb015-143">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="cb015-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="cb015-144">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="cb015-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="cb015-145">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="cb015-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="cb015-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-146">32 KB</span></span> | <span data-ttu-id="cb015-147">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="cb015-148">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb015-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cb015-149">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="cb015-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb015-150">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="cb015-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb015-151">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb015-152">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="cb015-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="cb015-153">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cb015-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb015-154">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-154">Option</span></span> | <span data-ttu-id="cb015-155">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-155">Default Value</span></span> | <span data-ttu-id="cb015-156">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb015-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-157">32 KB</span></span> | <span data-ttu-id="cb015-158">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="cb015-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="cb015-159">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb015-160">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb015-161">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb015-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-162">32 KB</span></span> | <span data-ttu-id="cb015-163">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="cb015-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="cb015-164">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb015-165">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-165">All Transports are enabled.</span></span> | <span data-ttu-id="cb015-166">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb015-167">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-167">See below.</span></span> | <span data-ttu-id="cb015-168">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb015-169">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-169">See below.</span></span> | <span data-ttu-id="cb015-170">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="cb015-171">0</span><span class="sxs-lookup"><span data-stu-id="cb015-171">0</span></span> | <span data-ttu-id="cb015-172">Określ minimalną wersję protokołu negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="cb015-173">Służy do ograniczania liczby klientów do nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="cb015-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="cb015-174">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb015-175">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-175">Option</span></span> | <span data-ttu-id="cb015-176">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-176">Default Value</span></span> | <span data-ttu-id="cb015-177">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb015-178">90 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-178">90 seconds</span></span> | <span data-ttu-id="cb015-179">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb015-180">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="cb015-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb015-181">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb015-182">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-182">Option</span></span> | <span data-ttu-id="cb015-183">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-183">Default Value</span></span> | <span data-ttu-id="cb015-184">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb015-185">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-185">5 seconds</span></span> | <span data-ttu-id="cb015-186">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="cb015-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb015-187">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="cb015-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb015-188">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb015-189">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="cb015-189">Configure client options</span></span>

<span data-ttu-id="cb015-190">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb015-191">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="cb015-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb015-192">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="cb015-192">Configure logging</span></span>

<span data-ttu-id="cb015-193">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="cb015-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb015-194">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="cb015-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb015-195">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="cb015-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-196">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="cb015-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb015-197">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="cb015-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb015-198">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="cb015-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb015-199">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb015-200">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="cb015-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb015-201">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb015-202">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="cb015-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="cb015-203">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="cb015-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="cb015-204">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="cb015-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="cb015-205">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="cb015-205">The following table lists the available log levels.</span></span> <span data-ttu-id="cb015-206">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="cb015-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="cb015-207">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="cb015-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="cb015-208">String</span><span class="sxs-lookup"><span data-stu-id="cb015-208">String</span></span>                      | <span data-ttu-id="cb015-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="cb015-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="cb015-210">`info`**lub**`information`</span><span class="sxs-lookup"><span data-stu-id="cb015-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="cb015-211">`warn`**lub**`warning`</span><span class="sxs-lookup"><span data-stu-id="cb015-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="cb015-212">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="cb015-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb015-213">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="cb015-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb015-214">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb015-215">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb015-216">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="cb015-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb015-217">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="cb015-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb015-218">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="cb015-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb015-219">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="cb015-219">Configure allowed transports</span></span>

<span data-ttu-id="cb015-220">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb015-221">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="cb015-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb015-222">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-222">All transports are enabled by default.</span></span>

<span data-ttu-id="cb015-223">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="cb015-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb015-224">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cb015-225">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="cb015-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="cb015-226">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb015-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="cb015-227">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb015-228">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="cb015-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb015-229">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="cb015-229">Configure bearer authentication</span></span>

<span data-ttu-id="cb015-230">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="cb015-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb015-231">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="cb015-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb015-232">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="cb015-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb015-233">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb015-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb015-234">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb015-235">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="cb015-236">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="cb015-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb015-237">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb015-238">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb015-239">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="cb015-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb015-240">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="cb015-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-241">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-242">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-242">Option</span></span> | <span data-ttu-id="cb015-243">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-243">Default value</span></span> | <span data-ttu-id="cb015-244">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb015-245">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-246">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-246">Timeout for server activity.</span></span> <span data-ttu-id="cb015-247">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-248">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-249">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb015-250">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-250">15 seconds</span></span> | <span data-ttu-id="cb015-251">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-252">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-253">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-254">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb015-255">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-255">15 seconds</span></span> | <span data-ttu-id="cb015-256">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-257">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-258">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cb015-259">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb015-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-261">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-261">Option</span></span> | <span data-ttu-id="cb015-262">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-262">Default value</span></span> | <span data-ttu-id="cb015-263">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb015-264">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-265">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-265">Timeout for server activity.</span></span> <span data-ttu-id="cb015-266">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb015-267">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-268">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cb015-269">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb015-270">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-271">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-272">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-273">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-273">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-274">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-274">Option</span></span> | <span data-ttu-id="cb015-275">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-275">Default value</span></span> | <span data-ttu-id="cb015-276">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb015-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb015-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb015-278">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-279">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-279">Timeout for server activity.</span></span> <span data-ttu-id="cb015-280">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-281">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-282">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb015-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-283">15 seconds</span></span> | <span data-ttu-id="cb015-284">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-285">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-286">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-287">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cb015-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cb015-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cb015-289">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb015-290">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-291">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-292">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb015-293">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="cb015-293">Configure additional options</span></span>

<span data-ttu-id="cb015-294">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="cb015-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-295">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-296">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="cb015-296">.NET Option</span></span> |  <span data-ttu-id="cb015-297">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-297">Default value</span></span> | <span data-ttu-id="cb015-298">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb015-299">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb015-300">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-301">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-302">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb015-303">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-303">Empty</span></span> | <span data-ttu-id="cb015-304">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="cb015-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb015-305">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-305">Empty</span></span> | <span data-ttu-id="cb015-306">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb015-307">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-307">Empty</span></span> | <span data-ttu-id="cb015-308">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb015-309">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-309">5 seconds</span></span> | <span data-ttu-id="cb015-310">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-310">WebSockets only.</span></span> <span data-ttu-id="cb015-311">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="cb015-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb015-312">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb015-313">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-313">Empty</span></span> | <span data-ttu-id="cb015-314">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb015-315">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb015-316">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb015-317">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="cb015-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb015-318">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="cb015-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb015-319">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="cb015-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb015-320">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb015-321">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb015-322">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="cb015-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb015-323">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb015-324">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="cb015-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb015-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-326">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-326">JavaScript Option</span></span> | <span data-ttu-id="cb015-327">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-327">Default Value</span></span> | <span data-ttu-id="cb015-328">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb015-329">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb015-330">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-331">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-332">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="cb015-333">Określa, czy poświadczenia będą wysyłane z żądaniem CORS.</span><span class="sxs-lookup"><span data-stu-id="cb015-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="cb015-334">Azure App Service używa plików cookie dla sesji programu Sticky i wymaga, aby ta opcja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="cb015-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="cb015-335">Aby uzyskać więcej informacji na temat mechanizmu CORS w programie SignalR , zobacz <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="cb015-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-336">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-336">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-337">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="cb015-337">Java Option</span></span> | <span data-ttu-id="cb015-338">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-338">Default Value</span></span> | <span data-ttu-id="cb015-339">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb015-340">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb015-341">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-342">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-343">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb015-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb015-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb015-345">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-345">Empty</span></span> | <span data-ttu-id="cb015-346">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb015-347">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb015-348">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb015-349">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb015-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb015-350">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cb015-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb015-351">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb015-352">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb015-353">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="cb015-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb015-354">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="cb015-355">`AddJsonProtocol`można dodać po elemencie [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb015-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cb015-356">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="cb015-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb015-357">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb015-358">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="cb015-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="cb015-359">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb015-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="cb015-360">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="cb015-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb015-361">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cb015-362">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="cb015-363">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="cb015-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="cb015-364">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="cb015-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb015-365">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-365">MessagePack serialization options</span></span>

<span data-ttu-id="cb015-366">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb015-367">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-368">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb015-369">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="cb015-369">Configure server options</span></span>

<span data-ttu-id="cb015-370">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="cb015-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb015-371">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-371">Option</span></span> | <span data-ttu-id="cb015-372">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-372">Default Value</span></span> | <span data-ttu-id="cb015-373">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cb015-374">30 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-374">30 seconds</span></span> | <span data-ttu-id="cb015-375">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="cb015-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cb015-376">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="cb015-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cb015-377">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="cb015-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cb015-378">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-378">15 seconds</span></span> | <span data-ttu-id="cb015-379">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="cb015-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb015-380">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-381">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb015-382">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-382">15 seconds</span></span> | <span data-ttu-id="cb015-383">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb015-384">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="cb015-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb015-385">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="cb015-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb015-386">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="cb015-386">All installed protocols</span></span> | <span data-ttu-id="cb015-387">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-387">Protocols supported by this hub.</span></span> <span data-ttu-id="cb015-388">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="cb015-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb015-389">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb015-390">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="cb015-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="cb015-391">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="cb015-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="cb015-392">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="cb015-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="cb015-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-393">32 KB</span></span> | <span data-ttu-id="cb015-394">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="cb015-395">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb015-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cb015-396">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="cb015-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb015-397">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="cb015-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb015-398">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb015-399">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="cb015-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="cb015-400">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cb015-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb015-401">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-401">Option</span></span> | <span data-ttu-id="cb015-402">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-402">Default Value</span></span> | <span data-ttu-id="cb015-403">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb015-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-404">32 KB</span></span> | <span data-ttu-id="cb015-405">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="cb015-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="cb015-406">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb015-407">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb015-408">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb015-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-409">32 KB</span></span> | <span data-ttu-id="cb015-410">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="cb015-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="cb015-411">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb015-412">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-412">All Transports are enabled.</span></span> | <span data-ttu-id="cb015-413">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb015-414">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-414">See below.</span></span> | <span data-ttu-id="cb015-415">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb015-416">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-416">See below.</span></span> | <span data-ttu-id="cb015-417">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="cb015-418">0</span><span class="sxs-lookup"><span data-stu-id="cb015-418">0</span></span> | <span data-ttu-id="cb015-419">Określ minimalną wersję protokołu negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="cb015-420">Służy do ograniczania liczby klientów do nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="cb015-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="cb015-421">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb015-422">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-422">Option</span></span> | <span data-ttu-id="cb015-423">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-423">Default Value</span></span> | <span data-ttu-id="cb015-424">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb015-425">90 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-425">90 seconds</span></span> | <span data-ttu-id="cb015-426">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb015-427">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="cb015-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb015-428">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb015-429">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-429">Option</span></span> | <span data-ttu-id="cb015-430">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-430">Default Value</span></span> | <span data-ttu-id="cb015-431">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb015-432">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-432">5 seconds</span></span> | <span data-ttu-id="cb015-433">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="cb015-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb015-434">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="cb015-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb015-435">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb015-436">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="cb015-436">Configure client options</span></span>

<span data-ttu-id="cb015-437">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb015-438">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="cb015-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb015-439">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="cb015-439">Configure logging</span></span>

<span data-ttu-id="cb015-440">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="cb015-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb015-441">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="cb015-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb015-442">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="cb015-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-443">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="cb015-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb015-444">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="cb015-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb015-445">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="cb015-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb015-446">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb015-447">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="cb015-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb015-448">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb015-449">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="cb015-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="cb015-450">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="cb015-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="cb015-451">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="cb015-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="cb015-452">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="cb015-452">The following table lists the available log levels.</span></span> <span data-ttu-id="cb015-453">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="cb015-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="cb015-454">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="cb015-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="cb015-455">String</span><span class="sxs-lookup"><span data-stu-id="cb015-455">String</span></span>                      | <span data-ttu-id="cb015-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="cb015-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="cb015-457">`info`**lub**`information`</span><span class="sxs-lookup"><span data-stu-id="cb015-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="cb015-458">`warn`**lub**`warning`</span><span class="sxs-lookup"><span data-stu-id="cb015-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="cb015-459">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="cb015-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb015-460">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="cb015-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb015-461">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb015-462">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb015-463">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="cb015-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb015-464">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="cb015-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb015-465">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="cb015-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb015-466">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="cb015-466">Configure allowed transports</span></span>

<span data-ttu-id="cb015-467">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb015-468">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="cb015-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb015-469">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-469">All transports are enabled by default.</span></span>

<span data-ttu-id="cb015-470">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="cb015-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb015-471">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cb015-472">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="cb015-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="cb015-473">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb015-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="cb015-474">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb015-475">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="cb015-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb015-476">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="cb015-476">Configure bearer authentication</span></span>

<span data-ttu-id="cb015-477">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="cb015-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb015-478">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="cb015-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb015-479">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="cb015-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb015-480">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb015-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb015-481">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb015-482">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="cb015-483">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="cb015-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb015-484">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb015-485">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb015-486">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="cb015-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb015-487">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="cb015-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-488">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-489">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-489">Option</span></span> | <span data-ttu-id="cb015-490">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-490">Default value</span></span> | <span data-ttu-id="cb015-491">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb015-492">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-493">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-493">Timeout for server activity.</span></span> <span data-ttu-id="cb015-494">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-495">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-496">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb015-497">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-497">15 seconds</span></span> | <span data-ttu-id="cb015-498">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-499">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-500">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-501">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb015-502">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-502">15 seconds</span></span> | <span data-ttu-id="cb015-503">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-504">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-505">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cb015-506">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb015-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-508">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-508">Option</span></span> | <span data-ttu-id="cb015-509">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-509">Default value</span></span> | <span data-ttu-id="cb015-510">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb015-511">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-512">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-512">Timeout for server activity.</span></span> <span data-ttu-id="cb015-513">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb015-514">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-515">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cb015-516">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb015-517">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-518">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-519">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-520">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-520">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-521">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-521">Option</span></span> | <span data-ttu-id="cb015-522">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-522">Default value</span></span> | <span data-ttu-id="cb015-523">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb015-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb015-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb015-525">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-526">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-526">Timeout for server activity.</span></span> <span data-ttu-id="cb015-527">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-528">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-529">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb015-530">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-530">15 seconds</span></span> | <span data-ttu-id="cb015-531">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-532">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-533">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-534">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cb015-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cb015-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cb015-536">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb015-537">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-538">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-539">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb015-540">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="cb015-540">Configure additional options</span></span>

<span data-ttu-id="cb015-541">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="cb015-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-542">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-543">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="cb015-543">.NET Option</span></span> |  <span data-ttu-id="cb015-544">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-544">Default value</span></span> | <span data-ttu-id="cb015-545">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb015-546">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb015-547">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-548">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-549">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb015-550">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-550">Empty</span></span> | <span data-ttu-id="cb015-551">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="cb015-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb015-552">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-552">Empty</span></span> | <span data-ttu-id="cb015-553">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb015-554">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-554">Empty</span></span> | <span data-ttu-id="cb015-555">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb015-556">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-556">5 seconds</span></span> | <span data-ttu-id="cb015-557">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-557">WebSockets only.</span></span> <span data-ttu-id="cb015-558">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="cb015-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb015-559">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb015-560">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-560">Empty</span></span> | <span data-ttu-id="cb015-561">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb015-562">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb015-563">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb015-564">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="cb015-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb015-565">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="cb015-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb015-566">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="cb015-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb015-567">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb015-568">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb015-569">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="cb015-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb015-570">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb015-571">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="cb015-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb015-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-573">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-573">JavaScript Option</span></span> | <span data-ttu-id="cb015-574">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-574">Default Value</span></span> | <span data-ttu-id="cb015-575">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb015-576">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb015-577">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-578">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-579">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-580">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-580">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-581">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="cb015-581">Java Option</span></span> | <span data-ttu-id="cb015-582">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-582">Default Value</span></span> | <span data-ttu-id="cb015-583">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb015-584">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb015-585">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-586">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-587">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb015-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb015-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb015-589">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-589">Empty</span></span> | <span data-ttu-id="cb015-590">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb015-591">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb015-592">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb015-593">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb015-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb015-594">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cb015-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb015-595">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb015-596">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb015-597">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="cb015-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb015-598">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="cb015-599">`AddJsonProtocol`można dodać po elemencie [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb015-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cb015-600">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="cb015-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb015-601">Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb015-602">Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="cb015-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="cb015-603">Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb015-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="cb015-604">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="cb015-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb015-605">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cb015-606">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="cb015-607">Przełącz do Newtonsoft.Jsna</span><span class="sxs-lookup"><span data-stu-id="cb015-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="cb015-608">Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="cb015-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb015-609">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-609">MessagePack serialization options</span></span>

<span data-ttu-id="cb015-610">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb015-611">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-612">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb015-613">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="cb015-613">Configure server options</span></span>

<span data-ttu-id="cb015-614">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="cb015-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb015-615">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-615">Option</span></span> | <span data-ttu-id="cb015-616">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-616">Default Value</span></span> | <span data-ttu-id="cb015-617">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cb015-618">30 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-618">30 seconds</span></span> | <span data-ttu-id="cb015-619">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="cb015-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cb015-620">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="cb015-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cb015-621">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="cb015-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cb015-622">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-622">15 seconds</span></span> | <span data-ttu-id="cb015-623">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="cb015-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb015-624">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-625">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb015-626">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-626">15 seconds</span></span> | <span data-ttu-id="cb015-627">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb015-628">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="cb015-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb015-629">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="cb015-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb015-630">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="cb015-630">All installed protocols</span></span> | <span data-ttu-id="cb015-631">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-631">Protocols supported by this hub.</span></span> <span data-ttu-id="cb015-632">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="cb015-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb015-633">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb015-634">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="cb015-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="cb015-635">Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów.</span><span class="sxs-lookup"><span data-stu-id="cb015-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="cb015-636">Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.</span><span class="sxs-lookup"><span data-stu-id="cb015-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="cb015-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-637">32 KB</span></span> | <span data-ttu-id="cb015-638">Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="cb015-639">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb015-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cb015-640">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="cb015-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb015-641">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="cb015-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb015-642">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb015-643">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="cb015-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="cb015-644">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cb015-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb015-645">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-645">Option</span></span> | <span data-ttu-id="cb015-646">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-646">Default Value</span></span> | <span data-ttu-id="cb015-647">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb015-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-648">32 KB</span></span> | <span data-ttu-id="cb015-649">Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia.</span><span class="sxs-lookup"><span data-stu-id="cb015-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="cb015-650">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb015-651">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb015-652">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb015-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-653">32 KB</span></span> | <span data-ttu-id="cb015-654">Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji.</span><span class="sxs-lookup"><span data-stu-id="cb015-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="cb015-655">Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb015-656">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-656">All Transports are enabled.</span></span> | <span data-ttu-id="cb015-657">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb015-658">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-658">See below.</span></span> | <span data-ttu-id="cb015-659">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb015-660">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-660">See below.</span></span> | <span data-ttu-id="cb015-661">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="cb015-662">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb015-663">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-663">Option</span></span> | <span data-ttu-id="cb015-664">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-664">Default Value</span></span> | <span data-ttu-id="cb015-665">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb015-666">90 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-666">90 seconds</span></span> | <span data-ttu-id="cb015-667">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb015-668">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="cb015-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb015-669">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb015-670">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-670">Option</span></span> | <span data-ttu-id="cb015-671">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-671">Default Value</span></span> | <span data-ttu-id="cb015-672">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb015-673">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-673">5 seconds</span></span> | <span data-ttu-id="cb015-674">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="cb015-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb015-675">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="cb015-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb015-676">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb015-677">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="cb015-677">Configure client options</span></span>

<span data-ttu-id="cb015-678">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb015-679">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="cb015-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb015-680">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="cb015-680">Configure logging</span></span>

<span data-ttu-id="cb015-681">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="cb015-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb015-682">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="cb015-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb015-683">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="cb015-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-684">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="cb015-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb015-685">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="cb015-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb015-686">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="cb015-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb015-687">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb015-688">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="cb015-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb015-689">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb015-690">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="cb015-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="cb015-691">Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="cb015-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="cb015-692">Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.</span><span class="sxs-lookup"><span data-stu-id="cb015-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="cb015-693">Poniższa tabela zawiera listę dostępnych poziomów dzienników.</span><span class="sxs-lookup"><span data-stu-id="cb015-693">The following table lists the available log levels.</span></span> <span data-ttu-id="cb015-694">Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany.</span><span class="sxs-lookup"><span data-stu-id="cb015-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="cb015-695">Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="cb015-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="cb015-696">String</span><span class="sxs-lookup"><span data-stu-id="cb015-696">String</span></span>                      | <span data-ttu-id="cb015-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="cb015-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="cb015-698">`info`**lub**`information`</span><span class="sxs-lookup"><span data-stu-id="cb015-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="cb015-699">`warn`**lub**`warning`</span><span class="sxs-lookup"><span data-stu-id="cb015-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="cb015-700">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="cb015-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb015-701">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="cb015-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb015-702">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb015-703">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb015-704">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="cb015-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb015-705">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="cb015-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb015-706">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="cb015-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb015-707">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="cb015-707">Configure allowed transports</span></span>

<span data-ttu-id="cb015-708">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb015-709">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="cb015-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb015-710">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-710">All transports are enabled by default.</span></span>

<span data-ttu-id="cb015-711">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="cb015-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb015-712">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cb015-713">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="cb015-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="cb015-714">W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb015-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="cb015-715">Klient Java domyślnie używa transportu usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb015-716">SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.</span><span class="sxs-lookup"><span data-stu-id="cb015-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb015-717">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="cb015-717">Configure bearer authentication</span></span>

<span data-ttu-id="cb015-718">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="cb015-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb015-719">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="cb015-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb015-720">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="cb015-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb015-721">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb015-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb015-722">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb015-723">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="cb015-724">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="cb015-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb015-725">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb015-726">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb015-727">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="cb015-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb015-728">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="cb015-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-729">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-730">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-730">Option</span></span> | <span data-ttu-id="cb015-731">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-731">Default value</span></span> | <span data-ttu-id="cb015-732">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb015-733">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-734">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-734">Timeout for server activity.</span></span> <span data-ttu-id="cb015-735">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-736">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-737">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb015-738">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-738">15 seconds</span></span> | <span data-ttu-id="cb015-739">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-740">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-741">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-742">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb015-743">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-743">15 seconds</span></span> | <span data-ttu-id="cb015-744">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-745">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-746">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cb015-747">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb015-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-749">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-749">Option</span></span> | <span data-ttu-id="cb015-750">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-750">Default value</span></span> | <span data-ttu-id="cb015-751">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb015-752">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-753">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-753">Timeout for server activity.</span></span> <span data-ttu-id="cb015-754">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb015-755">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-756">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cb015-757">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb015-758">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-759">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-760">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-761">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-761">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-762">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-762">Option</span></span> | <span data-ttu-id="cb015-763">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-763">Default value</span></span> | <span data-ttu-id="cb015-764">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb015-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb015-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb015-766">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-767">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-767">Timeout for server activity.</span></span> <span data-ttu-id="cb015-768">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-769">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-770">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb015-771">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-771">15 seconds</span></span> | <span data-ttu-id="cb015-772">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-773">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-774">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-775">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cb015-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cb015-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cb015-777">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb015-778">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-779">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-780">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb015-781">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="cb015-781">Configure additional options</span></span>

<span data-ttu-id="cb015-782">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="cb015-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-783">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-784">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="cb015-784">.NET Option</span></span> |  <span data-ttu-id="cb015-785">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-785">Default value</span></span> | <span data-ttu-id="cb015-786">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb015-787">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb015-788">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-789">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-790">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb015-791">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-791">Empty</span></span> | <span data-ttu-id="cb015-792">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="cb015-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb015-793">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-793">Empty</span></span> | <span data-ttu-id="cb015-794">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb015-795">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-795">Empty</span></span> | <span data-ttu-id="cb015-796">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb015-797">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-797">5 seconds</span></span> | <span data-ttu-id="cb015-798">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-798">WebSockets only.</span></span> <span data-ttu-id="cb015-799">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="cb015-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb015-800">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb015-801">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-801">Empty</span></span> | <span data-ttu-id="cb015-802">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb015-803">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb015-804">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb015-805">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="cb015-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb015-806">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="cb015-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb015-807">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="cb015-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb015-808">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb015-809">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb015-810">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="cb015-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb015-811">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb015-812">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="cb015-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb015-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-814">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-814">JavaScript Option</span></span> | <span data-ttu-id="cb015-815">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-815">Default Value</span></span> | <span data-ttu-id="cb015-816">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb015-817">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb015-818">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-819">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-820">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-821">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-821">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-822">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="cb015-822">Java Option</span></span> | <span data-ttu-id="cb015-823">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-823">Default Value</span></span> | <span data-ttu-id="cb015-824">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb015-825">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb015-826">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-827">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-828">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb015-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb015-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb015-830">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-830">Empty</span></span> | <span data-ttu-id="cb015-831">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb015-832">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb015-833">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb015-834">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb015-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb015-835">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cb015-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb015-836">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb015-837">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb015-838">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="cb015-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb015-839">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może zostać dodana po metodzie [addsygnalizująca](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w ramach `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="cb015-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cb015-840">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="cb015-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb015-841">Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb015-842">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="cb015-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="cb015-843">Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb015-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="cb015-844">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="cb015-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb015-845">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cb015-846">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb015-847">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-847">MessagePack serialization options</span></span>

<span data-ttu-id="cb015-848">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb015-849">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-850">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb015-851">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="cb015-851">Configure server options</span></span>

<span data-ttu-id="cb015-852">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="cb015-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb015-853">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-853">Option</span></span> | <span data-ttu-id="cb015-854">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-854">Default Value</span></span> | <span data-ttu-id="cb015-855">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cb015-856">30 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-856">30 seconds</span></span> | <span data-ttu-id="cb015-857">Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale).</span><span class="sxs-lookup"><span data-stu-id="cb015-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cb015-858">Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="cb015-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cb015-859">Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="cb015-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cb015-860">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-860">15 seconds</span></span> | <span data-ttu-id="cb015-861">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="cb015-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb015-862">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-863">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb015-864">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-864">15 seconds</span></span> | <span data-ttu-id="cb015-865">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb015-866">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="cb015-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb015-867">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="cb015-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb015-868">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="cb015-868">All installed protocols</span></span> | <span data-ttu-id="cb015-869">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-869">Protocols supported by this hub.</span></span> <span data-ttu-id="cb015-870">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="cb015-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb015-871">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb015-872">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="cb015-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="cb015-873">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb015-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cb015-874">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="cb015-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb015-875">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="cb015-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb015-876">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb015-877">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="cb015-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="cb015-878">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cb015-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb015-879">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-879">Option</span></span> | <span data-ttu-id="cb015-880">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-880">Default Value</span></span> | <span data-ttu-id="cb015-881">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb015-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-882">32 KB</span></span> | <span data-ttu-id="cb015-883">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer.</span><span class="sxs-lookup"><span data-stu-id="cb015-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="cb015-884">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb015-885">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb015-886">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb015-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-887">32 KB</span></span> | <span data-ttu-id="cb015-888">Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer.</span><span class="sxs-lookup"><span data-stu-id="cb015-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="cb015-889">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb015-890">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-890">All Transports are enabled.</span></span> | <span data-ttu-id="cb015-891">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb015-892">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-892">See below.</span></span> | <span data-ttu-id="cb015-893">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb015-894">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-894">See below.</span></span> | <span data-ttu-id="cb015-895">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="cb015-896">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb015-897">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-897">Option</span></span> | <span data-ttu-id="cb015-898">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-898">Default Value</span></span> | <span data-ttu-id="cb015-899">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb015-900">90 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-900">90 seconds</span></span> | <span data-ttu-id="cb015-901">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb015-902">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="cb015-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb015-903">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb015-904">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-904">Option</span></span> | <span data-ttu-id="cb015-905">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-905">Default Value</span></span> | <span data-ttu-id="cb015-906">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb015-907">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-907">5 seconds</span></span> | <span data-ttu-id="cb015-908">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="cb015-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb015-909">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="cb015-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb015-910">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb015-911">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="cb015-911">Configure client options</span></span>

<span data-ttu-id="cb015-912">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb015-913">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="cb015-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb015-914">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="cb015-914">Configure logging</span></span>

<span data-ttu-id="cb015-915">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="cb015-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb015-916">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="cb015-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb015-917">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="cb015-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-918">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="cb015-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb015-919">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="cb015-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb015-920">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="cb015-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb015-921">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb015-922">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="cb015-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb015-923">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb015-924">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="cb015-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb015-925">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="cb015-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb015-926">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="cb015-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb015-927">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb015-928">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb015-929">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="cb015-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb015-930">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="cb015-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb015-931">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="cb015-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb015-932">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="cb015-932">Configure allowed transports</span></span>

<span data-ttu-id="cb015-933">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb015-934">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="cb015-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb015-935">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-935">All transports are enabled by default.</span></span>

<span data-ttu-id="cb015-936">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="cb015-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb015-937">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cb015-938">W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.</span><span class="sxs-lookup"><span data-stu-id="cb015-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb015-939">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="cb015-939">Configure bearer authentication</span></span>

<span data-ttu-id="cb015-940">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="cb015-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb015-941">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="cb015-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb015-942">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="cb015-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb015-943">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb015-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb015-944">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb015-945">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="cb015-946">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="cb015-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb015-947">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb015-948">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb015-949">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="cb015-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb015-950">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="cb015-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-951">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-952">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-952">Option</span></span> | <span data-ttu-id="cb015-953">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-953">Default value</span></span> | <span data-ttu-id="cb015-954">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb015-955">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-956">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-956">Timeout for server activity.</span></span> <span data-ttu-id="cb015-957">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-958">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-959">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb015-960">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-960">15 seconds</span></span> | <span data-ttu-id="cb015-961">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-962">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-963">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-964">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb015-965">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-965">15 seconds</span></span> | <span data-ttu-id="cb015-966">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-967">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-968">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cb015-969">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb015-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-971">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-971">Option</span></span> | <span data-ttu-id="cb015-972">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-972">Default value</span></span> | <span data-ttu-id="cb015-973">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb015-974">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-975">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-975">Timeout for server activity.</span></span> <span data-ttu-id="cb015-976">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb015-977">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-978">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cb015-979">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb015-980">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-981">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-982">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-983">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-983">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-984">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-984">Option</span></span> | <span data-ttu-id="cb015-985">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-985">Default value</span></span> | <span data-ttu-id="cb015-986">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb015-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb015-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb015-988">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-989">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-989">Timeout for server activity.</span></span> <span data-ttu-id="cb015-990">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-991">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-992">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb015-993">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-993">15 seconds</span></span> | <span data-ttu-id="cb015-994">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-995">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-996">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-997">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cb015-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cb015-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cb015-999">15 sekund (15 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb015-1000">Określa interwał wysyłania komunikatów ping przez klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb015-1001">Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału.</span><span class="sxs-lookup"><span data-stu-id="cb015-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb015-1002">Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb015-1003">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="cb015-1003">Configure additional options</span></span>

<span data-ttu-id="cb015-1004">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="cb015-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-1006">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="cb015-1006">.NET Option</span></span> |  <span data-ttu-id="cb015-1007">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1007">Default value</span></span> | <span data-ttu-id="cb015-1008">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb015-1009">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb015-1010">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-1011">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-1012">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb015-1013">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1013">Empty</span></span> | <span data-ttu-id="cb015-1014">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="cb015-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb015-1015">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1015">Empty</span></span> | <span data-ttu-id="cb015-1016">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb015-1017">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1017">Empty</span></span> | <span data-ttu-id="cb015-1018">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb015-1019">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-1019">5 seconds</span></span> | <span data-ttu-id="cb015-1020">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-1020">WebSockets only.</span></span> <span data-ttu-id="cb015-1021">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="cb015-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb015-1022">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb015-1023">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1023">Empty</span></span> | <span data-ttu-id="cb015-1024">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb015-1025">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb015-1026">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb015-1027">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="cb015-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb015-1028">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="cb015-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb015-1029">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="cb015-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb015-1030">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb015-1031">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb015-1032">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="cb015-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb015-1033">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb015-1034">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="cb015-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb015-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-1036">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-1036">JavaScript Option</span></span> | <span data-ttu-id="cb015-1037">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1037">Default Value</span></span> | <span data-ttu-id="cb015-1038">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb015-1039">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb015-1040">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-1041">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-1042">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-1043">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-1044">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="cb015-1044">Java Option</span></span> | <span data-ttu-id="cb015-1045">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1045">Default Value</span></span> | <span data-ttu-id="cb015-1046">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb015-1047">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb015-1048">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-1049">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-1050">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb015-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb015-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb015-1052">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1052">Empty</span></span> | <span data-ttu-id="cb015-1053">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb015-1054">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb015-1055">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb015-1056">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb015-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb015-1057">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cb015-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb015-1058">Opcje serializacji JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb015-1059">ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb015-1060">Każdy protokół ma opcje konfiguracji serializacji.</span><span class="sxs-lookup"><span data-stu-id="cb015-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb015-1061">Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może zostać dodana po metodzie [addsygnalizująca](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w ramach `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="cb015-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cb015-1062">`AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt.</span><span class="sxs-lookup"><span data-stu-id="cb015-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb015-1063">Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb015-1064">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="cb015-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="cb015-1065">Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb015-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="cb015-1066">W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="cb015-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb015-1067">`Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cb015-1068">W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb015-1069">Opcje serializacji MessagePack</span><span class="sxs-lookup"><span data-stu-id="cb015-1069">MessagePack serialization options</span></span>

<span data-ttu-id="cb015-1070">Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb015-1071">Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="cb015-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-1072">W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb015-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb015-1073">Konfigurowanie opcji serwera</span><span class="sxs-lookup"><span data-stu-id="cb015-1073">Configure server options</span></span>

<span data-ttu-id="cb015-1074">W poniższej tabeli opisano opcje konfigurowania SignalR centrów:</span><span class="sxs-lookup"><span data-stu-id="cb015-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb015-1075">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-1075">Option</span></span> | <span data-ttu-id="cb015-1076">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1076">Default Value</span></span> | <span data-ttu-id="cb015-1077">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="cb015-1078">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-1078">15 seconds</span></span> | <span data-ttu-id="cb015-1079">Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="cb015-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb015-1080">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-1081">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb015-1082">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-1082">15 seconds</span></span> | <span data-ttu-id="cb015-1083">Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb015-1084">W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="cb015-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb015-1085">Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.</span><span class="sxs-lookup"><span data-stu-id="cb015-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb015-1086">Wszystkie zainstalowane protokoły</span><span class="sxs-lookup"><span data-stu-id="cb015-1086">All installed protocols</span></span> | <span data-ttu-id="cb015-1087">Protokoły obsługiwane przez to centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="cb015-1088">Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów.</span><span class="sxs-lookup"><span data-stu-id="cb015-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb015-1089">Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb015-1090">Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne.</span><span class="sxs-lookup"><span data-stu-id="cb015-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="cb015-1091">Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb015-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cb015-1092">Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="cb015-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb015-1093">Zaawansowane opcje konfiguracji HTTP</span><span class="sxs-lookup"><span data-stu-id="cb015-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb015-1094">Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb015-1095">Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="cb015-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="cb015-1096">W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cb015-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb015-1097">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-1097">Option</span></span> | <span data-ttu-id="cb015-1098">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1098">Default Value</span></span> | <span data-ttu-id="cb015-1099">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb015-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-1100">32 KB</span></span> | <span data-ttu-id="cb015-1101">Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer.</span><span class="sxs-lookup"><span data-stu-id="cb015-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="cb015-1102">Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb015-1103">Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb015-1104">Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum.</span><span class="sxs-lookup"><span data-stu-id="cb015-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb015-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="cb015-1105">32 KB</span></span> | <span data-ttu-id="cb015-1106">Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer.</span><span class="sxs-lookup"><span data-stu-id="cb015-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="cb015-1107">Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="cb015-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb015-1108">Wszystkie transporty są włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-1108">All Transports are enabled.</span></span> | <span data-ttu-id="cb015-1109">Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="cb015-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb015-1110">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-1110">See below.</span></span> | <span data-ttu-id="cb015-1111">Dodatkowe opcje specyficzne dla długiego transportu sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb015-1112">Sprawdź poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb015-1112">See below.</span></span> | <span data-ttu-id="cb015-1113">Dodatkowe opcje specyficzne dla transportu obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="cb015-1114">W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb015-1115">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-1115">Option</span></span> | <span data-ttu-id="cb015-1116">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1116">Default Value</span></span> | <span data-ttu-id="cb015-1117">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb015-1118">90 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-1118">90 seconds</span></span> | <span data-ttu-id="cb015-1119">Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb015-1120">Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej.</span><span class="sxs-lookup"><span data-stu-id="cb015-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb015-1121">Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cb015-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb015-1122">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-1122">Option</span></span> | <span data-ttu-id="cb015-1123">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1123">Default Value</span></span> | <span data-ttu-id="cb015-1124">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb015-1125">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-1125">5 seconds</span></span> | <span data-ttu-id="cb015-1126">Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="cb015-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb015-1127">Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową.</span><span class="sxs-lookup"><span data-stu-id="cb015-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb015-1128">Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb015-1129">Konfigurowanie opcji klienta</span><span class="sxs-lookup"><span data-stu-id="cb015-1129">Configure client options</span></span>

<span data-ttu-id="cb015-1130">Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb015-1131">Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="cb015-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb015-1132">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="cb015-1132">Configure logging</span></span>

<span data-ttu-id="cb015-1133">Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="cb015-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb015-1134">Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="cb015-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb015-1135">Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="cb015-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb015-1136">Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety.</span><span class="sxs-lookup"><span data-stu-id="cb015-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb015-1137">Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.</span><span class="sxs-lookup"><span data-stu-id="cb015-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb015-1138">Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="cb015-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb015-1139">Wywołaj `AddConsole` metodę rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="cb015-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb015-1140">W kliencie JavaScript `configureLogging` istnieje podobna Metoda.</span><span class="sxs-lookup"><span data-stu-id="cb015-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb015-1141">Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb015-1142">Dzienniki są zapisywane w oknie konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="cb015-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb015-1143">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="cb015-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb015-1144">Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="cb015-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb015-1145">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb015-1146">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb015-1147">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="cb015-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb015-1148">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="cb015-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb015-1149">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="cb015-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb015-1150">Skonfiguruj dozwolone transporty</span><span class="sxs-lookup"><span data-stu-id="cb015-1150">Configure allowed transports</span></span>

<span data-ttu-id="cb015-1151">Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb015-1152">Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów.</span><span class="sxs-lookup"><span data-stu-id="cb015-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb015-1153">Wszystkie transporty są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="cb015-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="cb015-1154">Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:</span><span class="sxs-lookup"><span data-stu-id="cb015-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb015-1155">W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb015-1156">Konfigurowanie uwierzytelniania okaziciela</span><span class="sxs-lookup"><span data-stu-id="cb015-1156">Configure bearer authentication</span></span>

<span data-ttu-id="cb015-1157">Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu.</span><span class="sxs-lookup"><span data-stu-id="cb015-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb015-1158">W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="cb015-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb015-1159">W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets).</span><span class="sxs-lookup"><span data-stu-id="cb015-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb015-1160">W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb015-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb015-1161">W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb015-1162">W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="cb015-1163">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="cb015-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb015-1164">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="cb015-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb015-1165">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="cb015-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb015-1166">Konfigurowanie opcji limitu czasu i utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="cb015-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb015-1167">W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="cb015-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-1169">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-1169">Option</span></span> | <span data-ttu-id="cb015-1170">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1170">Default value</span></span> | <span data-ttu-id="cb015-1171">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb015-1172">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-1173">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-1173">Timeout for server activity.</span></span> <span data-ttu-id="cb015-1174">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-1175">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-1176">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb015-1177">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-1177">15 seconds</span></span> | <span data-ttu-id="cb015-1178">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-1179">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb015-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb015-1180">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-1181">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="cb015-1182">W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.</span><span class="sxs-lookup"><span data-stu-id="cb015-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb015-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-1184">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-1184">Option</span></span> | <span data-ttu-id="cb015-1185">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1185">Default value</span></span> | <span data-ttu-id="cb015-1186">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb015-1187">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-1188">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-1188">Timeout for server activity.</span></span> <span data-ttu-id="cb015-1189">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb015-1190">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-1191">Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-1192">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-1193">Opcja</span><span class="sxs-lookup"><span data-stu-id="cb015-1193">Option</span></span> | <span data-ttu-id="cb015-1194">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1194">Default value</span></span> | <span data-ttu-id="cb015-1195">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb015-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb015-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb015-1197">30 sekund (30 000 MS)</span><span class="sxs-lookup"><span data-stu-id="cb015-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb015-1198">Limit czasu dla działania serwera.</span><span class="sxs-lookup"><span data-stu-id="cb015-1198">Timeout for server activity.</span></span> <span data-ttu-id="cb015-1199">Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-1200">Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="cb015-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb015-1201">Zalecana wartość to liczba z co najmniej podwójną `KeepAliveInterval` wartością serwera, aby umożliwić czas na nadejście poleceń ping.</span><span class="sxs-lookup"><span data-stu-id="cb015-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb015-1202">15 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-1202">15 seconds</span></span> | <span data-ttu-id="cb015-1203">Limit czasu początkowej uzgadniania z serwerem.</span><span class="sxs-lookup"><span data-stu-id="cb015-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb015-1204">Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="cb015-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb015-1205">Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci.</span><span class="sxs-lookup"><span data-stu-id="cb015-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb015-1206">Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="cb015-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb015-1207">Konfigurowanie opcji dodatkowych</span><span class="sxs-lookup"><span data-stu-id="cb015-1207">Configure additional options</span></span>

<span data-ttu-id="cb015-1208">Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:</span><span class="sxs-lookup"><span data-stu-id="cb015-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb015-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="cb015-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb015-1210">Opcja .NET</span><span class="sxs-lookup"><span data-stu-id="cb015-1210">.NET Option</span></span> |  <span data-ttu-id="cb015-1211">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1211">Default value</span></span> | <span data-ttu-id="cb015-1212">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb015-1213">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb015-1214">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-1215">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-1216">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb015-1217">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1217">Empty</span></span> | <span data-ttu-id="cb015-1218">Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań.</span><span class="sxs-lookup"><span data-stu-id="cb015-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb015-1219">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1219">Empty</span></span> | <span data-ttu-id="cb015-1220">Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb015-1221">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1221">Empty</span></span> | <span data-ttu-id="cb015-1222">Poświadczenia do wysyłania przy każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb015-1223">5 sekund</span><span class="sxs-lookup"><span data-stu-id="cb015-1223">5 seconds</span></span> | <span data-ttu-id="cb015-1224">Tylko obiekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-1224">WebSockets only.</span></span> <span data-ttu-id="cb015-1225">Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="cb015-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb015-1226">Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony.</span><span class="sxs-lookup"><span data-stu-id="cb015-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb015-1227">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1227">Empty</span></span> | <span data-ttu-id="cb015-1228">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb015-1229">Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb015-1230">Nieużywane na potrzeby połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb015-1231">Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr.</span><span class="sxs-lookup"><span data-stu-id="cb015-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb015-1232">Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="cb015-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb015-1233">**Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.**</span><span class="sxs-lookup"><span data-stu-id="cb015-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb015-1234">Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb015-1235">Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cb015-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb015-1236">Umożliwia to korzystanie z uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="cb015-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb015-1237">Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cb015-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb015-1238">Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="cb015-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb015-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb015-1240">Opcja języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb015-1240">JavaScript Option</span></span> | <span data-ttu-id="cb015-1241">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1241">Default Value</span></span> | <span data-ttu-id="cb015-1242">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb015-1243">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb015-1244">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-1245">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-1246">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb015-1247">Java</span><span class="sxs-lookup"><span data-stu-id="cb015-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="cb015-1248">Opcja Java</span><span class="sxs-lookup"><span data-stu-id="cb015-1248">Java Option</span></span> | <span data-ttu-id="cb015-1249">Wartość domyślna</span><span class="sxs-lookup"><span data-stu-id="cb015-1249">Default Value</span></span> | <span data-ttu-id="cb015-1250">Opis</span><span class="sxs-lookup"><span data-stu-id="cb015-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb015-1251">Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb015-1252">Ustaw tę wartość na `true` , aby pominąć krok negocjowania.</span><span class="sxs-lookup"><span data-stu-id="cb015-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb015-1253">**Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**.</span><span class="sxs-lookup"><span data-stu-id="cb015-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb015-1254">Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb015-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb015-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb015-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb015-1256">Pusty</span><span class="sxs-lookup"><span data-stu-id="cb015-1256">Empty</span></span> | <span data-ttu-id="cb015-1257">Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb015-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb015-1258">W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb015-1259">W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="cb015-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb015-1260">W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb015-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb015-1261">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cb015-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
