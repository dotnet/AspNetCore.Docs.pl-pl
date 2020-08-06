---
title: ASP.NET Core Blazor konfigurację modelu hostingu
author: guardrex
description: Zapoznaj się z dodatkowymi scenariuszami ASP.NET Core Blazor konfiguracji modelu hostingu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b32710e515d111b7dd6556f1db55082cd56a82b5
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819005"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="b672a-103">ASP.NET Core Blazor konfigurację modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="b672a-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="b672a-104">[Daniel Roth](https://github.com/danroth27), [MacKinnon Buck](https://github.com/MackinnonBuck)i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b672a-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b672a-105">W tym artykule opisano hostowanie konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="b672a-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="b672a-106">SignalRnegocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="b672a-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="b672a-107">*Ta sekcja ma zastosowanie do Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="b672a-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="b672a-108">Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak pliki cookie lub nagłówki uwierzytelniania http:</span><span class="sxs-lookup"><span data-stu-id="b672a-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="b672a-109">Użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawienia <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> na żądania między źródłami [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="b672a-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="b672a-110">Przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:</span><span class="sxs-lookup"><span data-stu-id="b672a-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="b672a-111">Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="b672a-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="b672a-112">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="b672a-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="b672a-113">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="b672a-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="b672a-114">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="b672a-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="b672a-115">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="b672a-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="b672a-116">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="b672a-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="b672a-117">Dodaj następujący do arkusza stylów aplikacji ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="b672a-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="b672a-118">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="b672a-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="b672a-119">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="b672a-119">CSS class</span></span>                       | <span data-ttu-id="b672a-120">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="b672a-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="b672a-121">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="b672a-121">A lost connection.</span></span> <span data-ttu-id="b672a-122">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="b672a-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="b672a-123">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="b672a-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="b672a-124">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="b672a-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="b672a-125">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="b672a-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="b672a-126">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="b672a-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="b672a-127">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="b672a-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="b672a-128">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="b672a-128">Reconnection rejected.</span></span> <span data-ttu-id="b672a-129">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="b672a-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="b672a-130">Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="b672a-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="b672a-131">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="b672a-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="b672a-132">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="b672a-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="b672a-133">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b672a-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="b672a-134">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="b672a-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="b672a-135">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="b672a-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="b672a-136">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="b672a-136">Render mode</span></span>

<span data-ttu-id="b672a-137">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="b672a-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="b672a-138">Blazor Serveraplikacje są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="b672a-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="b672a-139">Ta strona jest skonfigurowana na `_Host.cshtml` Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="b672a-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="b672a-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="b672a-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="b672a-141">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="b672a-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="b672a-142">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b672a-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="b672a-143">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="b672a-143">Render mode</span></span> | <span data-ttu-id="b672a-144">Opis</span><span class="sxs-lookup"><span data-stu-id="b672a-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="b672a-145">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b672a-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="b672a-146">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b672a-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="b672a-147">Renderuje znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b672a-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="b672a-148">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="b672a-148">Output from the component isn't included.</span></span> <span data-ttu-id="b672a-149">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b672a-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="b672a-150">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="b672a-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="b672a-151">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="b672a-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="b672a-152">Konfigurowanie SignalR klienta dla Blazor Server aplikacji</span><span class="sxs-lookup"><span data-stu-id="b672a-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="b672a-153">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="b672a-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="b672a-154">Skonfiguruj SignalR klienta używanego przez Blazor Server aplikacje w `Pages/_Host.cshtml` pliku.</span><span class="sxs-lookup"><span data-stu-id="b672a-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="b672a-155">Umieść skrypt wywołujący `Blazor.start` po `_framework/blazor.server.js` skrypcie i wewnątrz `</body>` znacznika.</span><span class="sxs-lookup"><span data-stu-id="b672a-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="b672a-156">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="b672a-156">Logging</span></span>

<span data-ttu-id="b672a-157">Aby skonfigurować SignalR Rejestrowanie klientów:</span><span class="sxs-lookup"><span data-stu-id="b672a-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="b672a-158">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="b672a-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b672a-159">Przekaż obiekt konfiguracji ( `configureSignalR` ), który jest wywoływany `configureLogging` z poziomem rejestrowania w konstruktorze klienta.</span><span class="sxs-lookup"><span data-stu-id="b672a-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
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

<span data-ttu-id="b672a-160">W poprzednim przykładzie `information` jest to odpowiednik poziomu dziennika <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b672a-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="b672a-161">Modyfikowanie procedury obsługi ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="b672a-161">Modify the reconnection handler</span></span>

<span data-ttu-id="b672a-162">Zdarzenia połączenia obwodu procedury obsługi ponownego połączenia można modyfikować dla zachowań niestandardowych, takich jak:</span><span class="sxs-lookup"><span data-stu-id="b672a-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="b672a-163">Powiadomienie użytkownika, jeśli połączenie zostało porzucone.</span><span class="sxs-lookup"><span data-stu-id="b672a-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="b672a-164">W celu przeprowadzenia rejestrowania (od klienta), gdy obwód jest połączony.</span><span class="sxs-lookup"><span data-stu-id="b672a-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="b672a-165">Aby zmodyfikować zdarzenia połączenia:</span><span class="sxs-lookup"><span data-stu-id="b672a-165">To modify the connection events:</span></span>

* <span data-ttu-id="b672a-166">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="b672a-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b672a-167">Zarejestruj wywołania zwrotne dla zmian połączenia dla porzuconych połączeń ( `onConnectionDown` ) i ustanowionych/ponownie ustanowionych połączeń ( `onConnectionUp` ).</span><span class="sxs-lookup"><span data-stu-id="b672a-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="b672a-168">**Oba** `onConnectionDown` elementy i `onConnectionUp` musi być określony.</span><span class="sxs-lookup"><span data-stu-id="b672a-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="b672a-169">Dostosuj liczbę ponownych prób ponownego połączenia i interwał</span><span class="sxs-lookup"><span data-stu-id="b672a-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="b672a-170">Aby dostosować liczbę ponownych prób ponownego połączenia i interwał:</span><span class="sxs-lookup"><span data-stu-id="b672a-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="b672a-171">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="b672a-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b672a-172">Ustaw liczbę ponownych prób ( `maxRetries` ) i okres w milisekundach dozwolony dla każdej próby ponowienia prób ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="b672a-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="b672a-173">Ukryj lub Zastąp ekran ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="b672a-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="b672a-174">Aby ukryć ekran Ponowne łączenie:</span><span class="sxs-lookup"><span data-stu-id="b672a-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="b672a-175">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="b672a-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b672a-176">Ustaw dla programu obsługi ponownego połączenia `_reconnectionDisplay` pusty obiekt ( `{}` lub `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="b672a-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="b672a-177">Aby zastąpić ekran ponownego połączenia, ustaw `_reconnectionDisplay` w poprzednim przykładzie element do wyświetlenia:</span><span class="sxs-lookup"><span data-stu-id="b672a-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="b672a-178">Symbol zastępczy `{ELEMENT ID}` jest identyfikatorem elementu HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="b672a-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="b672a-179">Wpływ na `<head>` elementy tagów HTML</span><span class="sxs-lookup"><span data-stu-id="b672a-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="b672a-180">*Ta sekcja dotyczy Blazor WebAssembly i Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="b672a-180">*This section applies to Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="b672a-181">Gdy renderowane,, `Title` `Link` i `Meta` składniki dodają lub aktualizują dane w `<head>` elementach tagów HTML:</span><span class="sxs-lookup"><span data-stu-id="b672a-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="b672a-182">W powyższym przykładzie symbole zastępcze dla `{TITLE}` , `{URL}` , i `{DESCRIPTION}` są wartościami ciągów, Razor zmiennymi lub Razor wyrażeniami.</span><span class="sxs-lookup"><span data-stu-id="b672a-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="b672a-183">Stosuje się następujące cechy:</span><span class="sxs-lookup"><span data-stu-id="b672a-183">The following characteristics apply:</span></span>

* <span data-ttu-id="b672a-184">Renderowanie wstępne po stronie serwera jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="b672a-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="b672a-185">`Value`Parametr jest jedynym prawidłowym parametrem `Title` składnika.</span><span class="sxs-lookup"><span data-stu-id="b672a-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="b672a-186">Atrybuty HTML podane w `Meta` `Link` składnikach i są przechwytywane w [dodatkowych atrybutach](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) i przekazywane do renderowanego tagu HTML.</span><span class="sxs-lookup"><span data-stu-id="b672a-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="b672a-187">W przypadku wielu `Title` składników tytuł strony odzwierciedla `Value` ostatni `Title` renderowany składnik.</span><span class="sxs-lookup"><span data-stu-id="b672a-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="b672a-188">Jeśli wiele `Meta` lub `Link` składniki są dołączone do identycznych atrybutów, istnieje dokładnie jeden tag HTML renderowany dla `Meta` lub `Link` składnika.</span><span class="sxs-lookup"><span data-stu-id="b672a-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="b672a-189">Dwa `Meta` lub `Link` składniki nie mogą odwoływać się do tego samego renderowanego tagu HTML.</span><span class="sxs-lookup"><span data-stu-id="b672a-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="b672a-190">Zmiany parametrów istniejących `Meta` lub `Link` składników są uwzględniane w ich RENDEROWANE Tagi HTML.</span><span class="sxs-lookup"><span data-stu-id="b672a-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="b672a-191">Gdy `Link` składniki lub `Meta` nie są już renderowane i w rezultacie są usuwane przez platformę, RENDEROWANE Tagi HTML są usuwane.</span><span class="sxs-lookup"><span data-stu-id="b672a-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="b672a-192">Gdy jeden ze składników platformy jest używany w składniku podrzędnym, renderowany tag HTML ma wpływ na dowolny składnik podrzędny składnika nadrzędnego, o ile jest renderowany składnik podrzędny zawierający składnik Framework.</span><span class="sxs-lookup"><span data-stu-id="b672a-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="b672a-193">Rozróżnienie między użyciem jednego z tych składników platformy w składniku podrzędnym i umieszczenie znacznika HTML w `wwwroot/index.html` lub `Pages/_Host.cshtml` polega na tym, że RENDEROWANY tag HTML składnika Framework:</span><span class="sxs-lookup"><span data-stu-id="b672a-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="b672a-194">Mogą być modyfikowane przez stan aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b672a-194">Can be modified by application state.</span></span> <span data-ttu-id="b672a-195">Nie można zmodyfikować oznakowanego języka HTML w stanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b672a-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="b672a-196">Jest usuwany z kodu HTML, `<head>` gdy składnik nadrzędny nie jest już renderowany.</span><span class="sxs-lookup"><span data-stu-id="b672a-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b672a-197">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b672a-197">Additional resources</span></span>

* <xref:fundamentals/logging/index>
