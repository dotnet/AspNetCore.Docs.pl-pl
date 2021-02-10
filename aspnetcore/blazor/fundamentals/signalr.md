---
title: Blazor SignalR Wskazówki dotyczące ASP.NET Core
author: guardrex
description: Dowiedz się, jak konfigurować połączenia i zarządzać nimi Blazor SignalR .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
no-loc:
- appsettings.json
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
uid: blazor/fundamentals/signalr
ms.openlocfilehash: 31cd68baa0fbc773f09fe3a1b37091b2dfc0b0a0
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100110035"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="015b0-103">Blazor SignalR Wskazówki dotyczące ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="015b0-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="015b0-104">[Daniel Roth](https://github.com/danroth27), [MacKinnon Buck](https://github.com/MackinnonBuck)i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="015b0-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="015b0-105">Ogólne wskazówki dotyczące konfiguracji ASP.NET Core SignalR można znaleźć w tematach w <xref:signalr/introduction> obszarze dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="015b0-105">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="015b0-106">Aby skonfigurować SignalR [dodanie do Blazor WebAssembly rozwiązania hostowanego](xref:tutorials/signalr-blazor), zobacz <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="015b0-106">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="015b0-107">Opcje procedury obsługi obwodu</span><span class="sxs-lookup"><span data-stu-id="015b0-107">Circuit handler options</span></span>

<span data-ttu-id="015b0-108">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="015b0-108">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="015b0-109">Skonfiguruj Blazor Server obwód z <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> pokazanymi w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="015b0-109">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="015b0-110">Opcja</span><span class="sxs-lookup"><span data-stu-id="015b0-110">Option</span></span> | <span data-ttu-id="015b0-111">Domyślne</span><span class="sxs-lookup"><span data-stu-id="015b0-111">Default</span></span> | <span data-ttu-id="015b0-112">Opis</span><span class="sxs-lookup"><span data-stu-id="015b0-112">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="015b0-113">Wysyłaj szczegółowe komunikaty o wyjątkach do języka JavaScript, gdy wystąpił nieobsługiwany wyjątek w obwodzie lub kiedy wywołanie metody .NET za pomocą kodu JS Interop spowoduje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="015b0-113">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="015b0-114">100</span><span class="sxs-lookup"><span data-stu-id="015b0-114">100</span></span> | <span data-ttu-id="015b0-115">Maksymalna liczba odłączonych obwodów, które dany serwer przechowuje w pamięci w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="015b0-115">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="015b0-116">3 minuty</span><span class="sxs-lookup"><span data-stu-id="015b0-116">3 minutes</span></span> | <span data-ttu-id="015b0-117">Maksymalny czas przechowywania połączonego obwodu w pamięci przed jego usunięciem.</span><span class="sxs-lookup"><span data-stu-id="015b0-117">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="015b0-118">1 minuta</span><span class="sxs-lookup"><span data-stu-id="015b0-118">1 minute</span></span> | <span data-ttu-id="015b0-119">Maksymalny czas oczekiwania serwera przed upływem limitu czasu asynchronicznego wywołania funkcji JavaScript.</span><span class="sxs-lookup"><span data-stu-id="015b0-119">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="015b0-120">10</span><span class="sxs-lookup"><span data-stu-id="015b0-120">10</span></span> | <span data-ttu-id="015b0-121">Maksymalna liczba niepotwierdzonych partii renderowania, które serwer przechowuje w pamięci na obwód w danym momencie do obsługi niezawodnego ponownego łączenia.</span><span class="sxs-lookup"><span data-stu-id="015b0-121">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="015b0-122">Po osiągnięciu limitu serwer przestaje tworzyć nowe partie renderowania do momentu potwierdzenia co najmniej jednej partii przez klienta.</span><span class="sxs-lookup"><span data-stu-id="015b0-122">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="015b0-123">Skonfiguruj opcje w programie `Startup.ConfigureServices` przy użyciu opcji delegat <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> .</span><span class="sxs-lookup"><span data-stu-id="015b0-123">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="015b0-124">Poniższy przykład przypisuje domyślne wartości opcji pokazanych w powyższej tabeli:</span><span class="sxs-lookup"><span data-stu-id="015b0-124">The following example assigns the default option values shown in the preceding table:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="015b0-125">Aby skonfigurować <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , użyj <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> z <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> .</span><span class="sxs-lookup"><span data-stu-id="015b0-125">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="015b0-126">Aby uzyskać opisy opcji, zobacz <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="015b0-126">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="015b0-127">Poniższy przykład przypisuje domyślne wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="015b0-127">The following example assigns the default option values:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="015b0-128">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="015b0-128">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="015b0-129">*Ta sekcja ma zastosowanie do Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="015b0-129">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="015b0-130">Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak cookie nagłówki uwierzytelniania s lub http:</span><span class="sxs-lookup"><span data-stu-id="015b0-130">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="015b0-131">Użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawienia <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> na żądania między źródłami [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="015b0-131">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="015b0-132">Przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:</span><span class="sxs-lookup"><span data-stu-id="015b0-132">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="015b0-133">Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="015b0-133">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="015b0-134">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="015b0-134">Reflect the connection state in the UI</span></span>

<span data-ttu-id="015b0-135">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="015b0-135">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="015b0-136">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="015b0-136">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="015b0-137">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="015b0-137">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="015b0-138">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="015b0-138">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="015b0-139">Dodaj następujący do arkusza stylów aplikacji ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="015b0-139">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="015b0-140">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="015b0-140">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="015b0-141">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="015b0-141">CSS class</span></span>                       | <span data-ttu-id="015b0-142">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="015b0-142">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="015b0-143">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="015b0-143">A lost connection.</span></span> <span data-ttu-id="015b0-144">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="015b0-144">The client is attempting to reconnect.</span></span> <span data-ttu-id="015b0-145">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="015b0-145">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="015b0-146">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="015b0-146">An active connection is re-established to the server.</span></span> <span data-ttu-id="015b0-147">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="015b0-147">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="015b0-148">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="015b0-148">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="015b0-149">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="015b0-149">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="015b0-150">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="015b0-150">Reconnection rejected.</span></span> <span data-ttu-id="015b0-151">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="015b0-151">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="015b0-152">Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="015b0-152">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="015b0-153">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="015b0-153">This connection state may result when:</span></span><ul><li><span data-ttu-id="015b0-154">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="015b0-154">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="015b0-155">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="015b0-155">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="015b0-156">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="015b0-156">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="015b0-157">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="015b0-157">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="015b0-158">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="015b0-158">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="015b0-159">*Ta sekcja ma zastosowanie do hostowanych Blazor WebAssembly i Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="015b0-159">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="015b0-160">Blazor aplikacje są domyślnie skonfigurowane w taki sposób, aby wyprerender interfejs użytkownika na serwerze.</span><span class="sxs-lookup"><span data-stu-id="015b0-160">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="015b0-161">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="015b0-161">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="015b0-162">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="015b0-162">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="015b0-163">Blazor Server aplikacje są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="015b0-163">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="015b0-164">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="015b0-164">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="015b0-165">Inicjowanie Blazor obwodu</span><span class="sxs-lookup"><span data-stu-id="015b0-165">Initialize the Blazor circuit</span></span>

<span data-ttu-id="015b0-166">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="015b0-166">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="015b0-167">Skonfiguruj ręczne uruchamianie Blazor Server [ SignalR obwodu](xref:blazor/hosting-models#circuits) aplikacji w `Pages/_Host.cshtml` pliku:</span><span class="sxs-lookup"><span data-stu-id="015b0-167">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="015b0-168">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="015b0-168">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="015b0-169">Umieść skrypt wywołujący `Blazor.start` po `blazor.server.js` tagu skryptu i wewnątrz taga zamykającego `</body>` .</span><span class="sxs-lookup"><span data-stu-id="015b0-169">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="015b0-170">Gdy `autostart` jest wyłączone, dowolny aspekt aplikacji, która nie zależy od obwodu, zwykle działa.</span><span class="sxs-lookup"><span data-stu-id="015b0-170">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="015b0-171">Na przykład Routing po stronie klienta działa.</span><span class="sxs-lookup"><span data-stu-id="015b0-171">For example, client-side routing is operational.</span></span> <span data-ttu-id="015b0-172">Jednak każdy aspekt, który zależy od obwodu, nie działa do momentu `Blazor.start` wywołania.</span><span class="sxs-lookup"><span data-stu-id="015b0-172">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="015b0-173">Zachowanie aplikacji jest nieprzewidywalne bez ustalonego obwodu.</span><span class="sxs-lookup"><span data-stu-id="015b0-173">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="015b0-174">Na przykład metody komponentów nie są wykonywane podczas odłączania obwodu.</span><span class="sxs-lookup"><span data-stu-id="015b0-174">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="015b0-175">Inicjuj Blazor , gdy dokument jest gotowy</span><span class="sxs-lookup"><span data-stu-id="015b0-175">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="015b0-176">Aby zainicjować Blazor aplikację, gdy dokument jest gotowy:</span><span class="sxs-lookup"><span data-stu-id="015b0-176">To initialize the Blazor app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="015b0-177">Łańcuch z `Promise` wynikami ręcznego uruchamiania</span><span class="sxs-lookup"><span data-stu-id="015b0-177">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="015b0-178">Aby wykonać dodatkowe zadania, takie jak Inicjalizacja międzyoperacyjna JS, użyj `then` do łańcucha z `Promise` wynikami ręcznego Blazor uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="015b0-178">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-signalr-client"></a><span data-ttu-id="015b0-179">Konfigurowanie SignalR klienta programu</span><span class="sxs-lookup"><span data-stu-id="015b0-179">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="015b0-180">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="015b0-180">Logging</span></span>

<span data-ttu-id="015b0-181">Aby skonfigurować SignalR Rejestrowanie klienta, należy przekazać obiekt konfiguracji ( `configureSignalR` ), który wywołuje `configureLogging` z poziomem rejestrowania w konstruktorze klienta:</span><span class="sxs-lookup"><span data-stu-id="015b0-181">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="015b0-182">W poprzednim przykładzie `information` jest to odpowiednik poziomu dziennika <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="015b0-182">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="015b0-183">Modyfikowanie procedury obsługi ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="015b0-183">Modify the reconnection handler</span></span>

<span data-ttu-id="015b0-184">Zdarzenia połączenia obwodu procedury obsługi ponownego połączenia można modyfikować dla zachowań niestandardowych, takich jak:</span><span class="sxs-lookup"><span data-stu-id="015b0-184">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="015b0-185">Powiadomienie użytkownika, jeśli połączenie zostało porzucone.</span><span class="sxs-lookup"><span data-stu-id="015b0-185">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="015b0-186">W celu przeprowadzenia rejestrowania (od klienta), gdy obwód jest połączony.</span><span class="sxs-lookup"><span data-stu-id="015b0-186">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="015b0-187">Aby zmodyfikować zdarzenia połączenia, zarejestruj wywołania zwrotne dla następujących zmian połączeń:</span><span class="sxs-lookup"><span data-stu-id="015b0-187">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="015b0-188">Porzucone użycie połączeń `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="015b0-188">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="015b0-189">Nawiązane/ponownie nawiązane połączenia `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="015b0-189">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="015b0-190">**Oba** `onConnectionDown` elementy i `onConnectionUp` muszą być określone:</span><span class="sxs-lookup"><span data-stu-id="015b0-190">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="015b0-191">Dostosuj liczbę ponownych prób ponownego połączenia i interwał</span><span class="sxs-lookup"><span data-stu-id="015b0-191">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="015b0-192">Aby dostosować liczbę ponownych prób ponownego połączenia i interwał, ustaw liczbę ponownych prób ( `maxRetries` ) i okres w milisekundach dozwolony dla każdej próbnej próby ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="015b0-192">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="015b0-193">Ukryj lub Zastąp ekran ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="015b0-193">Hide or replace the reconnection display</span></span>

<span data-ttu-id="015b0-194">Aby ukryć ekran ponowne połączenie, ustaw dla programu obsługi ponownego połączenia `_reconnectionDisplay` pusty obiekt ( `{}` lub `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="015b0-194">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="015b0-195">Aby zastąpić ekran ponownego połączenia, ustaw `_reconnectionDisplay` w poprzednim przykładzie element do wyświetlenia:</span><span class="sxs-lookup"><span data-stu-id="015b0-195">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="015b0-196">Symbol zastępczy `{ELEMENT ID}` jest identyfikatorem elementu HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="015b0-196">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="015b0-197">Dostosuj opóźnienie przed wyświetleniem ekranu ponownego połączenia przez ustawienie `transition-delay` właściwości w kodzie CSS aplikacji ( `wwwroot/css/site.css` ) dla elementu modalnego.</span><span class="sxs-lookup"><span data-stu-id="015b0-197">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="015b0-198">W poniższym przykładzie ustawiono opóźnienie przejścia z 500 ms (domyślnie) do 1 000 MS (1 sekunda):</span><span class="sxs-lookup"><span data-stu-id="015b0-198">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="015b0-199">Odłączanie Blazor obwodu od klienta</span><span class="sxs-lookup"><span data-stu-id="015b0-199">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="015b0-200">Domyślnie Blazor obwód jest odłączony, gdy wyzwalane jest [ `unload` zdarzenie strony](https://developer.mozilla.org/docs/Web/API/Window/unload_event) .</span><span class="sxs-lookup"><span data-stu-id="015b0-200">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="015b0-201">Aby odłączyć obwód dla innych scenariuszy na kliencie, wywołaj `Blazor.disconnect` w odpowiedniej procedurze obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="015b0-201">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="015b0-202">W poniższym przykładzie obwód jest odłączony, gdy strona jest ukryta ([ `pagehide` zdarzenie](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="015b0-202">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="015b0-203">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="015b0-203">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="015b0-204">Blazor Server zdarzenia ponownego połączenia i zdarzenia cyklu życia składnika</span><span class="sxs-lookup"><span data-stu-id="015b0-204">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
