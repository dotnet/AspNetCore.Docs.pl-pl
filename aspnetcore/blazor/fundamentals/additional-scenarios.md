---
title: ASP.NET Core Blazor konfigurację modelu hostingu
author: guardrex
description: Zapoznaj się z dodatkowymi scenariuszami ASP.NET Core Blazor konfiguracji modelu hostingu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 726aafd2bf5d3469c30ebce1e4eea8ed8ec8d58e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103878"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="ceef9-103">ASP.NET Core Blazor konfigurację modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="ceef9-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="ceef9-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ceef9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ceef9-105">W tym artykule opisano hostowanie konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="ceef9-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="ceef9-106">negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="ceef9-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="ceef9-107">*Ta sekcja dotyczy Blazor zestawu webassembly.*</span><span class="sxs-lookup"><span data-stu-id="ceef9-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="ceef9-108">Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak pliki cookie lub nagłówki uwierzytelniania http:</span><span class="sxs-lookup"><span data-stu-id="ceef9-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="ceef9-109">Użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawienia <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> dla żądań [pobrania](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) między źródłami:</span><span class="sxs-lookup"><span data-stu-id="ceef9-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="ceef9-110">Przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:</span><span class="sxs-lookup"><span data-stu-id="ceef9-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="ceef9-111">Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="ceef9-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="ceef9-112">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="ceef9-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="ceef9-113">*Ta sekcja dotyczy Blazor serwera programu.*</span><span class="sxs-lookup"><span data-stu-id="ceef9-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ceef9-114">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="ceef9-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="ceef9-115">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="ceef9-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="ceef9-116">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` stronie *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="ceef9-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="ceef9-117">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="ceef9-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="ceef9-118">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="ceef9-118">CSS class</span></span>                       | <span data-ttu-id="ceef9-119">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="ceef9-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="ceef9-120">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="ceef9-120">A lost connection.</span></span> <span data-ttu-id="ceef9-121">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="ceef9-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="ceef9-122">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="ceef9-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="ceef9-123">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="ceef9-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="ceef9-124">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="ceef9-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="ceef9-125">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="ceef9-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="ceef9-126">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="ceef9-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="ceef9-127">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="ceef9-127">Reconnection rejected.</span></span> <span data-ttu-id="ceef9-128">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="ceef9-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="ceef9-129">Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="ceef9-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="ceef9-130">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="ceef9-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="ceef9-131">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="ceef9-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="ceef9-132">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ceef9-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="ceef9-133">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="ceef9-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="ceef9-134">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="ceef9-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="ceef9-135">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="ceef9-135">Render mode</span></span>

<span data-ttu-id="ceef9-136">*Ta sekcja dotyczy Blazor serwera programu.*</span><span class="sxs-lookup"><span data-stu-id="ceef9-136">*This section applies to Blazor Server.*</span></span>

Blazor<span data-ttu-id="ceef9-137">Aplikacje serwera są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="ceef9-137"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="ceef9-138">Ta konfiguracja jest ustawiana na stronie *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="ceef9-138">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="ceef9-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="ceef9-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="ceef9-140">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="ceef9-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="ceef9-141">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ceef9-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="ceef9-142">Opis</span><span class="sxs-lookup"><span data-stu-id="ceef9-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="ceef9-143">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="ceef9-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ceef9-144">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ceef9-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="ceef9-145">Renderuje znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="ceef9-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ceef9-146">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="ceef9-146">Output from the component isn't included.</span></span> <span data-ttu-id="ceef9-147">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ceef9-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="ceef9-148">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="ceef9-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="ceef9-149">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="ceef9-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="ceef9-150">Konfigurowanie SignalR klienta dla Blazor aplikacji serwerowych</span><span class="sxs-lookup"><span data-stu-id="ceef9-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="ceef9-151">*Ta sekcja dotyczy Blazor serwera programu.*</span><span class="sxs-lookup"><span data-stu-id="ceef9-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ceef9-152">Czasami trzeba skonfigurować SignalR klienta używanego przez Blazor aplikacje serwera.</span><span class="sxs-lookup"><span data-stu-id="ceef9-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="ceef9-153">Na przykład możesz chcieć skonfigurować rejestrowanie na SignalR kliencie, aby zdiagnozować problem z połączeniem.</span><span class="sxs-lookup"><span data-stu-id="ceef9-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="ceef9-154">Aby skonfigurować SignalR klienta w pliku *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ceef9-154">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="ceef9-155">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="ceef9-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ceef9-156">Wywoływanie `Blazor.start` i przekazywanie obiektu konfiguracji, który określa SignalR Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="ceef9-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ceef9-157">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ceef9-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>
