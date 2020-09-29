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

*Ta sekcja ma zastosowanie do Blazor Server .*

Blazor Server aplikacje są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem. Ta strona jest skonfigurowana na `_Host.cshtml` Razor stronie:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> Określa, czy składnik:

* Jest wstępnie renderowany na stronie.
* Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.

| Tryb renderowania | Opis |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla Blazor Server aplikacji. Dane wyjściowe ze składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje składnik do statycznego kodu HTML. |

Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.

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
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
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

## <a name="influence-html-head-tag-elements"></a>Wpływ na `<head>` elementy tagów HTML

*Ta sekcja ma zastosowanie do nadchodzącego ASP.NET Core 5,0 wersji Blazor WebAssembly i Blazor Server .*

Gdy renderowane,, `Title` `Link` i `Meta` składniki dodają lub aktualizują dane w `<head>` elementach tagów HTML:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

W powyższym przykładzie symbole zastępcze dla `{TITLE}` , `{URL}` , i `{DESCRIPTION}` są wartościami ciągów, Razor zmiennymi lub Razor wyrażeniami.

Stosuje się następujące cechy:

* Renderowanie wstępne po stronie serwera jest obsługiwane.
* `Value`Parametr jest jedynym prawidłowym parametrem `Title` składnika.
* Atrybuty HTML podane w `Meta` `Link` składnikach i są przechwytywane w [dodatkowych atrybutach](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) i przekazywane do renderowanego tagu HTML.
* W przypadku wielu `Title` składników tytuł strony odzwierciedla `Value` ostatni `Title` renderowany składnik.
* Jeśli wiele `Meta` lub `Link` składniki są dołączone do identycznych atrybutów, istnieje dokładnie jeden tag HTML renderowany dla `Meta` lub `Link` składnika. Dwa `Meta` lub `Link` składniki nie mogą odwoływać się do tego samego renderowanego tagu HTML.
* Zmiany parametrów istniejących `Meta` lub `Link` składników są uwzględniane w ich RENDEROWANE Tagi HTML.
* Gdy `Link` składniki lub `Meta` nie są już renderowane i w rezultacie są usuwane przez platformę, RENDEROWANE Tagi HTML są usuwane.

Gdy jeden ze składników platformy jest używany w składniku podrzędnym, renderowany tag HTML ma wpływ na dowolny składnik podrzędny składnika nadrzędnego, o ile jest renderowany składnik podrzędny zawierający składnik Framework. Rozróżnienie między użyciem jednego z tych składników platformy w składniku podrzędnym i umieszczenie znacznika HTML w `wwwroot/index.html` lub `Pages/_Host.cshtml` polega na tym, że RENDEROWANY tag HTML składnika Framework:

* Mogą być modyfikowane przez stan aplikacji. Nie można zmodyfikować oznakowanego języka HTML w stanie aplikacji.
* Jest usuwany z kodu HTML, `<head>` gdy składnik nadrzędny nie jest już renderowany.

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

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/logging/index>
