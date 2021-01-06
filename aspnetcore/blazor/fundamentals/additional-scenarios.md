---
title: ASP.NET Core Blazor konfigurację modelu hostingu
author: guardrex
description: Zapoznaj się z dodatkowymi scenariuszami ASP.NET Core Blazor konfiguracji modelu hostingu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b7fc3710fe5ad1efba907edf98f590a42e2a83ae
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485878"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>ASP.NET Core Blazor konfigurację modelu hostingu

[Daniel Roth](https://github.com/danroth27), [MacKinnon Buck](https://github.com/MackinnonBuck)i [Luke Latham](https://github.com/guardrex)

W tym artykule opisano hostowanie konfiguracji modelu.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>SignalR negocjowanie między źródłami na potrzeby uwierzytelniania

*Ta sekcja ma zastosowanie do Blazor WebAssembly .*

Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak cookie nagłówki uwierzytelniania s lub http:

* Użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawienia <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> na żądania między źródłami [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

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

* Przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Odzwierciedlanie stanu połączenia w interfejsie użytkownika

*Ta sekcja ma zastosowanie do Blazor Server .*

Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie. Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.

Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stronie:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Dodaj następujący do arkusza stylów aplikacji ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ):

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.

| Klasa CSS                       | Wskazuje&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Utracono połączenie. Klient próbuje ponownie nawiązać połączenie. Pokaż modalne. |
| `components-reconnect-hide`     | Aktywne połączenie zostanie ponownie nawiązane z serwerem. Ukryj modalne. |
| `components-reconnect-failed`   | Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci. Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Odrzucono ponowne połączenie. Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony. Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` . Ten stan połączenia może skutkować tym, że:<ul><li>Wystąpił awaria w obwodzie po stronie serwera.</li><li>Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika. Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</li><li>Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</li></ul> |

## <a name="render-mode"></a>Tryb renderowania

::: moniker range=">= aspnetcore-5.0"

*Ta sekcja ma zastosowanie do hostowanych Blazor WebAssembly i Blazor Server .*

Blazor aplikacje są domyślnie skonfigurowane w taki sposób, aby wyprerender interfejs użytkownika na serwerze. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

*Ta sekcja ma zastosowanie do Blazor Server .*

Blazor Server aplikacje są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a>Inicjowanie Blazor obwodu

*Ta sekcja ma zastosowanie do Blazor Server .*

Skonfiguruj ręczne uruchamianie Blazor Server [ SignalR obwodu](xref:blazor/hosting-models#circuits) aplikacji w `Pages/_Host.cshtml` pliku:

* Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.
* Umieść skrypt wywołujący `Blazor.start` po `blazor.server.js` tagu skryptu i wewnątrz taga zamykającego `</body>` .

Gdy `autostart` jest wyłączone, dowolny aspekt aplikacji, która nie zależy od obwodu, zwykle działa. Na przykład Routing po stronie klienta działa. Jednak każdy aspekt, który zależy od obwodu, nie działa do momentu `Blazor.start` wywołania. Zachowanie aplikacji jest nieprzewidywalne bez ustalonego obwodu. Na przykład metody komponentów nie są wykonywane podczas odłączania obwodu.

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a>Inicjuj Blazor , gdy dokument jest gotowy

Aby zainicjować Blazor aplikację, gdy dokument jest gotowy:

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>Łańcuch z `Promise` wynikami ręcznego uruchamiania

Aby wykonać dodatkowe zadania, takie jak Inicjalizacja międzyoperacyjna JS, użyj `then` do łańcucha z `Promise` wynikami ręcznego Blazor uruchamiania aplikacji:

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

### <a name="configure-the-no-locsignalr-client"></a>Konfigurowanie SignalR klienta programu

#### <a name="logging"></a>Rejestrowanie

Aby skonfigurować SignalR Rejestrowanie klienta, należy przekazać obiekt konfiguracji ( `configureSignalR` ), który wywołuje `configureLogging` z poziomem rejestrowania w konstruktorze klienta:

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

W poprzednim przykładzie `information` jest to odpowiednik poziomu dziennika <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .

### <a name="modify-the-reconnection-handler"></a>Modyfikowanie procedury obsługi ponownego połączenia

Zdarzenia połączenia obwodu procedury obsługi ponownego połączenia można modyfikować dla zachowań niestandardowych, takich jak:

* Powiadomienie użytkownika, jeśli połączenie zostało porzucone.
* W celu przeprowadzenia rejestrowania (od klienta), gdy obwód jest połączony.

Aby zmodyfikować zdarzenia połączenia, zarejestruj wywołania zwrotne dla następujących zmian połączeń:

* Porzucone użycie połączeń `onConnectionDown` .
* Nawiązane/ponownie nawiązane połączenia `onConnectionUp` .

**Oba** `onConnectionDown` elementy i `onConnectionUp` muszą być określone:

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Dostosuj liczbę ponownych prób ponownego połączenia i interwał

Aby dostosować liczbę ponownych prób ponownego połączenia i interwał, ustaw liczbę ponownych prób ( `maxRetries` ) i okres w milisekundach dozwolony dla każdej próbnej próby ( `retryIntervalMilliseconds` ):

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

## <a name="hide-or-replace-the-reconnection-display"></a>Ukryj lub Zastąp ekran ponownego połączenia

Aby ukryć ekran ponowne połączenie, ustaw dla programu obsługi ponownego połączenia `_reconnectionDisplay` pusty obiekt ( `{}` lub `new Object()` ):

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

Aby zastąpić ekran ponownego połączenia, ustaw `_reconnectionDisplay` w poprzednim przykładzie element do wyświetlenia:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

Symbol zastępczy `{ELEMENT ID}` jest identyfikatorem elementu HTML do wyświetlenia.

::: moniker range=">= aspnetcore-5.0"

Dostosuj opóźnienie przed wyświetleniem ekranu ponownego połączenia przez ustawienie `transition-delay` właściwości w kodzie CSS aplikacji ( `wwwroot/css/site.css` ) dla elementu modalnego. W poniższym przykładzie ustawiono opóźnienie przejścia z 500 ms (domyślnie) do 1 000 MS (1 sekunda):

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a>Odłączanie Blazor obwodu od klienta

Domyślnie Blazor obwód jest odłączony, gdy wyzwalane jest [ `unload` zdarzenie strony](https://developer.mozilla.org/docs/Web/API/Window/unload_event) . Aby odłączyć obwód dla innych scenariuszy na kliencie, wywołaj `Blazor.disconnect` w odpowiedniej procedurze obsługi zdarzeń. W poniższym przykładzie obwód jest odłączony, gdy strona jest ukryta ([ `pagehide` zdarzenie](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

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

## <a name="static-files"></a>Pliki statyczne

*Ta sekcja ma zastosowanie do Blazor Server .*

Aby utworzyć dodatkowe mapowania plików z <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> lub skonfigurować inne <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , użyj **jednej** z poniższych metod. W poniższych przykładach `{EXTENSION}` symbol zastępczy to rozszerzenie pliku, a `{CONTENT TYPE}` symbol zastępczy to typ zawartości.

* Skonfiguruj opcje za pomocą [iniekcji zależności (di)](xref:blazor/fundamentals/dependency-injection) w `Startup.ConfigureServices` ( `Startup.cs` ) przy użyciu <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :

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

  Ponieważ takie podejście konfiguruje tego samego dostawcę plików, który jest używany do obsługi programu `blazor.server.js` , należy się upewnić, że Konfiguracja niestandardowa nie zakłóca działania programu `blazor.server.js` . Na przykład nie należy usuwać mapowania dla plików JavaScript przez skonfigurowanie dostawcy za pomocą programu `provider.Mappings.Remove(".js")` .

* Użyj dwóch wywołań <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> w programie `Startup.Configure` ( `Startup.cs` ):
  * Skonfiguruj niestandardowego dostawcę plików przy pierwszym wywołaniu przy użyciu <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .
  * Drugie oprogramowanie pośredniczące służy `blazor.server.js` do korzystania z domyślnej konfiguracji plików statycznych dostarczonych przez Blazor platformę.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* Można uniknąć zakłócania działania programu przy `_framework/blazor.server.js` użyciu programu <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> do wykonywania niestandardowego oprogramowania pośredniczącego plików statycznych:

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/logging/index>
* [Blazor Server zdarzenia ponownego połączenia i zdarzenia cyklu życia składnika](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
