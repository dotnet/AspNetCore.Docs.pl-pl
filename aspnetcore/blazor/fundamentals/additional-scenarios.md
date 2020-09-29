---
title: ASP.NET Core Blazor konfigurację modelu hostingu
author: guardrex
description: Zapoznaj się z dodatkowymi scenariuszami ASP.NET Core Blazor konfiguracji modelu hostingu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 236d95e54b772ea522911421084ec0d9022c45ff
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424142"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="1ebc5-103">ASP.NET Core Blazor konfigurację modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="1ebc5-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="1ebc5-104">[Daniel Roth](https://github.com/danroth27), [MacKinnon Buck](https://github.com/MackinnonBuck)i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1ebc5-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1ebc5-105">W tym artykule opisano hostowanie konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="1ebc5-106">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1ebc5-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="1ebc5-107">*Ta sekcja ma zastosowanie do Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="1ebc5-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="1ebc5-108">Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak cookie nagłówki uwierzytelniania s lub http:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="1ebc5-109">Użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawienia <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> na żądania między źródłami [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="1ebc5-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="1ebc5-110">Przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="1ebc5-111">Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="1ebc5-112">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="1ebc5-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="1ebc5-113">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="1ebc5-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="1ebc5-114">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="1ebc5-115">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="1ebc5-116">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="1ebc5-117">Dodaj następujący do arkusza stylów aplikacji ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="1ebc5-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="1ebc5-118">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="1ebc5-119">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="1ebc5-119">CSS class</span></span>                       | <span data-ttu-id="1ebc5-120">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="1ebc5-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="1ebc5-121">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-121">A lost connection.</span></span> <span data-ttu-id="1ebc5-122">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="1ebc5-123">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="1ebc5-124">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="1ebc5-125">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="1ebc5-126">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="1ebc5-127">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="1ebc5-128">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-128">Reconnection rejected.</span></span> <span data-ttu-id="1ebc5-129">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="1ebc5-130">Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="1ebc5-131">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="1ebc5-132">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="1ebc5-133">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="1ebc5-134">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="1ebc5-135">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="1ebc5-136">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="1ebc5-136">Render mode</span></span>

<span data-ttu-id="1ebc5-137">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="1ebc5-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="1ebc5-138">Blazor Server aplikacje są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="1ebc5-139">Ta strona jest skonfigurowana na `_Host.cshtml` Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="1ebc5-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="1ebc5-141">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="1ebc5-142">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="1ebc5-143">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="1ebc5-143">Render mode</span></span> | <span data-ttu-id="1ebc5-144">Opis</span><span class="sxs-lookup"><span data-stu-id="1ebc5-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="1ebc5-145">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="1ebc5-146">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="1ebc5-147">Renderuje znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="1ebc5-148">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-148">Output from the component isn't included.</span></span> <span data-ttu-id="1ebc5-149">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="1ebc5-150">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="1ebc5-151">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="1ebc5-152">Inicjowanie Blazor obwodu</span><span class="sxs-lookup"><span data-stu-id="1ebc5-152">Initialize the Blazor circuit</span></span>

<span data-ttu-id="1ebc5-153">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="1ebc5-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="1ebc5-154">Skonfiguruj ręczne uruchamianie Blazor Server [ SignalR obwodu](xref:blazor/hosting-models#circuits) aplikacji w `Pages/_Host.cshtml` pliku:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-154">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="1ebc5-155">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="1ebc5-156">Umieść skrypt wywołujący `Blazor.start` po `blazor.server.js` tagu skryptu i wewnątrz taga zamykającego `</body>` .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-156">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="1ebc5-157">Gdy `autostart` jest wyłączone, dowolny aspekt aplikacji, która nie zależy od obwodu, zwykle działa.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="1ebc5-158">Na przykład Routing po stronie klienta działa.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="1ebc5-159">Jednak każdy aspekt, który zależy od obwodu, nie działa do momentu `Blazor.start` wywołania.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-159">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="1ebc5-160">Zachowanie aplikacji jest nieprzewidywalne bez ustalonego obwodu.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="1ebc5-161">Na przykład metody komponentów nie są wykonywane podczas odłączania obwodu.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="1ebc5-162">Inicjuj Blazor , gdy dokument jest gotowy</span><span class="sxs-lookup"><span data-stu-id="1ebc5-162">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="1ebc5-163">Aby zainicjować Blazor aplikację, gdy dokument jest gotowy:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-163">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="1ebc5-164">Łańcuch z `Promise` wynikami ręcznego uruchamiania</span><span class="sxs-lookup"><span data-stu-id="1ebc5-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="1ebc5-165">Aby wykonać dodatkowe zadania, takie jak Inicjalizacja międzyoperacyjna JS, użyj `then` do łańcucha z `Promise` wynikami ręcznego Blazor uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="1ebc5-166">Konfigurowanie SignalR klienta programu</span><span class="sxs-lookup"><span data-stu-id="1ebc5-166">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="1ebc5-167">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="1ebc5-167">Logging</span></span>

<span data-ttu-id="1ebc5-168">Aby skonfigurować SignalR Rejestrowanie klienta, należy przekazać obiekt konfiguracji ( `configureSignalR` ), który wywołuje `configureLogging` z poziomem rejestrowania w konstruktorze klienta:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-168">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="1ebc5-169">W poprzednim przykładzie `information` jest to odpowiednik poziomu dziennika <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="1ebc5-170">Modyfikowanie procedury obsługi ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="1ebc5-170">Modify the reconnection handler</span></span>

<span data-ttu-id="1ebc5-171">Zdarzenia połączenia obwodu procedury obsługi ponownego połączenia można modyfikować dla zachowań niestandardowych, takich jak:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="1ebc5-172">Powiadomienie użytkownika, jeśli połączenie zostało porzucone.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="1ebc5-173">W celu przeprowadzenia rejestrowania (od klienta), gdy obwód jest połączony.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="1ebc5-174">Aby zmodyfikować zdarzenia połączenia, zarejestruj wywołania zwrotne dla następujących zmian połączeń:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="1ebc5-175">Porzucone użycie połączeń `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="1ebc5-176">Nawiązane/ponownie nawiązane połączenia `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="1ebc5-177">**Oba** `onConnectionDown` elementy i `onConnectionUp` muszą być określone:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="1ebc5-178">Dostosuj liczbę ponownych prób ponownego połączenia i interwał</span><span class="sxs-lookup"><span data-stu-id="1ebc5-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="1ebc5-179">Aby dostosować liczbę ponownych prób ponownego połączenia i interwał, ustaw liczbę ponownych prób ( `maxRetries` ) i okres w milisekundach dozwolony dla każdej próbnej próby ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="1ebc5-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="1ebc5-180">Ukryj lub Zastąp ekran ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="1ebc5-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="1ebc5-181">Aby ukryć ekran ponowne połączenie, ustaw dla programu obsługi ponownego połączenia `_reconnectionDisplay` pusty obiekt ( `{}` lub `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="1ebc5-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="1ebc5-182">Aby zastąpić ekran ponownego połączenia, ustaw `_reconnectionDisplay` w poprzednim przykładzie element do wyświetlenia:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="1ebc5-183">Symbol zastępczy `{ELEMENT ID}` jest identyfikatorem elementu HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1ebc5-184">Dostosuj opóźnienie przed wyświetleniem ekranu ponownego połączenia przez ustawienie `transition-delay` właściwości w kodzie CSS aplikacji ( `wwwroot/css/site.css` ) dla elementu modalnego.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="1ebc5-185">W poniższym przykładzie ustawiono opóźnienie przejścia z 500 ms (domyślnie) do 1 000 MS (1 sekunda):</span><span class="sxs-lookup"><span data-stu-id="1ebc5-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="1ebc5-186">Odłączanie Blazor obwodu od klienta</span><span class="sxs-lookup"><span data-stu-id="1ebc5-186">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="1ebc5-187">Domyślnie Blazor obwód jest odłączony, gdy wyzwalane jest [ `unload` zdarzenie strony](https://developer.mozilla.org/docs/Web/API/Window/unload_event) .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-187">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="1ebc5-188">Aby odłączyć obwód dla innych scenariuszy na kliencie, wywołaj `Blazor.disconnect` w odpowiedniej procedurze obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-188">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="1ebc5-189">W poniższym przykładzie obwód jest odłączony, gdy strona jest ukryta ([ `pagehide` zdarzenie](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="1ebc5-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="1ebc5-190">Wpływ na `<head>` elementy tagów HTML</span><span class="sxs-lookup"><span data-stu-id="1ebc5-190">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="1ebc5-191">*Ta sekcja ma zastosowanie do nadchodzącego ASP.NET Core 5,0 wersji Blazor WebAssembly i Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="1ebc5-191">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="1ebc5-192">Gdy renderowane,, `Title` `Link` i `Meta` składniki dodają lub aktualizują dane w `<head>` elementach tagów HTML:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-192">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="1ebc5-193">W powyższym przykładzie symbole zastępcze dla `{TITLE}` , `{URL}` , i `{DESCRIPTION}` są wartościami ciągów, Razor zmiennymi lub Razor wyrażeniami.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-193">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="1ebc5-194">Stosuje się następujące cechy:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-194">The following characteristics apply:</span></span>

* <span data-ttu-id="1ebc5-195">Renderowanie wstępne po stronie serwera jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-195">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="1ebc5-196">`Value`Parametr jest jedynym prawidłowym parametrem `Title` składnika.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-196">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="1ebc5-197">Atrybuty HTML podane w `Meta` `Link` składnikach i są przechwytywane w [dodatkowych atrybutach](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) i przekazywane do renderowanego tagu HTML.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-197">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="1ebc5-198">W przypadku wielu `Title` składników tytuł strony odzwierciedla `Value` ostatni `Title` renderowany składnik.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-198">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="1ebc5-199">Jeśli wiele `Meta` lub `Link` składniki są dołączone do identycznych atrybutów, istnieje dokładnie jeden tag HTML renderowany dla `Meta` lub `Link` składnika.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-199">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="1ebc5-200">Dwa `Meta` lub `Link` składniki nie mogą odwoływać się do tego samego renderowanego tagu HTML.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-200">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="1ebc5-201">Zmiany parametrów istniejących `Meta` lub `Link` składników są uwzględniane w ich RENDEROWANE Tagi HTML.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-201">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="1ebc5-202">Gdy `Link` składniki lub `Meta` nie są już renderowane i w rezultacie są usuwane przez platformę, RENDEROWANE Tagi HTML są usuwane.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-202">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="1ebc5-203">Gdy jeden ze składników platformy jest używany w składniku podrzędnym, renderowany tag HTML ma wpływ na dowolny składnik podrzędny składnika nadrzędnego, o ile jest renderowany składnik podrzędny zawierający składnik Framework.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-203">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="1ebc5-204">Rozróżnienie między użyciem jednego z tych składników platformy w składniku podrzędnym i umieszczenie znacznika HTML w `wwwroot/index.html` lub `Pages/_Host.cshtml` polega na tym, że RENDEROWANY tag HTML składnika Framework:</span><span class="sxs-lookup"><span data-stu-id="1ebc5-204">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="1ebc5-205">Mogą być modyfikowane przez stan aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-205">Can be modified by application state.</span></span> <span data-ttu-id="1ebc5-206">Nie można zmodyfikować oznakowanego języka HTML w stanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-206">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="1ebc5-207">Jest usuwany z kodu HTML, `<head>` gdy składnik nadrzędny nie jest już renderowany.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-207">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="1ebc5-208">Pliki statyczne</span><span class="sxs-lookup"><span data-stu-id="1ebc5-208">Static files</span></span>

<span data-ttu-id="1ebc5-209">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="1ebc5-209">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="1ebc5-210">Aby utworzyć dodatkowe mapowania plików z <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> lub skonfigurować inne <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , użyj **jednej** z poniższych metod.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-210">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="1ebc5-211">W poniższych przykładach `{EXTENSION}` symbol zastępczy to rozszerzenie pliku, a `{CONTENT TYPE}` symbol zastępczy to typ zawartości.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-211">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="1ebc5-212">Skonfiguruj opcje za pomocą [iniekcji zależności (di)](xref:blazor/fundamentals/dependency-injection) w `Startup.ConfigureServices` ( `Startup.cs` ) przy użyciu <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="1ebc5-212">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="1ebc5-213">Ponieważ takie podejście konfiguruje tego samego dostawcę plików, który jest używany do obsługi programu `blazor.server.js` , należy się upewnić, że Konfiguracja niestandardowa nie zakłóca działania programu `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-213">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="1ebc5-214">Na przykład nie należy usuwać mapowania dla plików JavaScript przez skonfigurowanie dostawcy za pomocą programu `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-214">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="1ebc5-215">Użyj dwóch wywołań <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> w programie `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="1ebc5-215">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="1ebc5-216">Skonfiguruj niestandardowego dostawcę plików przy pierwszym wywołaniu przy użyciu <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="1ebc5-216">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="1ebc5-217">Drugie oprogramowanie pośredniczące służy `blazor.server.js` do korzystania z domyślnej konfiguracji plików statycznych dostarczonych przez Blazor platformę.</span><span class="sxs-lookup"><span data-stu-id="1ebc5-217">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

## <a name="additional-resources"></a><span data-ttu-id="1ebc5-218">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1ebc5-218">Additional resources</span></span>

* <xref:fundamentals/logging/index>
