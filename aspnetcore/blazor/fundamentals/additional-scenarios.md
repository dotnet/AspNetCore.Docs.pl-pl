---
title: ASP.NET Core Blazor konfigurację modelu hostingu
author: guardrex
description: Zapoznaj się z dodatkowymi scenariuszami ASP.NET Core Blazor konfiguracji modelu hostingu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: e62cb2ab865fbf57166d5ec3d1344183c00c2095
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059844"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="fdbb7-103">ASP.NET Core Blazor konfigurację modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="fdbb7-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="fdbb7-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fdbb7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fdbb7-105">W tym artykule opisano hostowanie konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="fdbb7-106">negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="fdbb7-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="fdbb7-107">*Ta sekcja ma zastosowanie do Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="fdbb7-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="fdbb7-108">Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak pliki cookie lub nagłówki uwierzytelniania http:</span><span class="sxs-lookup"><span data-stu-id="fdbb7-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="fdbb7-109">Użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawienia <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> na żądania między źródłami [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="fdbb7-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="fdbb7-110">Przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:</span><span class="sxs-lookup"><span data-stu-id="fdbb7-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="fdbb7-111">Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="fdbb7-112">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="fdbb7-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="fdbb7-113">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="fdbb7-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="fdbb7-114">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="fdbb7-115">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="fdbb7-116">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="fdbb7-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="fdbb7-117">Dodaj następujący do arkusza stylów aplikacji ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="fdbb7-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="fdbb7-118">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="fdbb7-119">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="fdbb7-119">CSS class</span></span>                       | <span data-ttu-id="fdbb7-120">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="fdbb7-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="fdbb7-121">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-121">A lost connection.</span></span> <span data-ttu-id="fdbb7-122">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="fdbb7-123">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="fdbb7-124">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="fdbb7-125">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="fdbb7-126">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="fdbb7-127">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="fdbb7-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="fdbb7-128">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-128">Reconnection rejected.</span></span> <span data-ttu-id="fdbb7-129">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="fdbb7-130">Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="fdbb7-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="fdbb7-131">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="fdbb7-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="fdbb7-132">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="fdbb7-133">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="fdbb7-134">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="fdbb7-135">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="fdbb7-136">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="fdbb7-136">Render mode</span></span>

<span data-ttu-id="fdbb7-137">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="fdbb7-137">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="fdbb7-138">aplikacje są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-138"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="fdbb7-139">Ta strona jest skonfigurowana na `_Host.cshtml` Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="fdbb7-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="fdbb7-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="fdbb7-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="fdbb7-141">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="fdbb7-142">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="fdbb7-143">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="fdbb7-143">Render mode</span></span> | <span data-ttu-id="fdbb7-144">Opis</span><span class="sxs-lookup"><span data-stu-id="fdbb7-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="fdbb7-145">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="fdbb7-146">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="fdbb7-147">Renderuje znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="fdbb7-148">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-148">Output from the component isn't included.</span></span> <span data-ttu-id="fdbb7-149">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="fdbb7-150">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="fdbb7-151">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="fdbb7-152">Konfigurowanie SignalR klienta dla Blazor Server aplikacji</span><span class="sxs-lookup"><span data-stu-id="fdbb7-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="fdbb7-153">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="fdbb7-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="fdbb7-154">Czasami trzeba skonfigurować SignalR klienta używanego przez Blazor Server aplikacje.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="fdbb7-155">Na przykład możesz chcieć skonfigurować rejestrowanie na SignalR kliencie, aby zdiagnozować problem z połączeniem.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="fdbb7-156">Aby skonfigurować SignalR klienta w `Pages/_Host.cshtml` pliku:</span><span class="sxs-lookup"><span data-stu-id="fdbb7-156">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="fdbb7-157">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="fdbb7-158">Wywoływanie `Blazor.start` i przekazywanie obiektu konfiguracji, który określa SignalR Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="fdbb7-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="fdbb7-159">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="fdbb7-159">Additional resources</span></span>

* <xref:fundamentals/logging/index>
