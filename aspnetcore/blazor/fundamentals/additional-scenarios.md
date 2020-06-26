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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 236dffd829bcd7c30ae1145242ce07cd8e9857e6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402952"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor konfigurację modelu hostingu

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

W tym artykule opisano hostowanie konfiguracji modelu.

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalRnegocjowanie między źródłami na potrzeby uwierzytelniania

*Ta sekcja ma zastosowanie do Blazor WebAssembly .*

Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak pliki cookie lub nagłówki uwierzytelniania http:

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

W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.

| Klasa CSS                       | Wskazuje&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Utracono połączenie. Klient próbuje ponownie nawiązać połączenie. Pokaż modalne. |
| `components-reconnect-hide`     | Aktywne połączenie zostanie ponownie nawiązane z serwerem. Ukryj modalne. |
| `components-reconnect-failed`   | Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci. Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Odrzucono ponowne połączenie. Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony. Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` . Ten stan połączenia może skutkować tym, że:<ul><li>Wystąpił awaria w obwodzie po stronie serwera.</li><li>Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika. Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</li><li>Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</li></ul> |

## <a name="render-mode"></a>Tryb renderowania

*Ta sekcja ma zastosowanie do Blazor Server .*

Blazor Serveraplikacje są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem. Ta strona jest skonfigurowana na `_Host.cshtml` Razor stronie:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Określa, czy składnik:

* Jest wstępnie renderowany na stronie.
* Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Opis |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla Blazor Server aplikacji. Dane wyjściowe ze składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje składnik do statycznego kodu HTML. |

Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Konfigurowanie SignalR klienta dla Blazor Server aplikacji

*Ta sekcja ma zastosowanie do Blazor Server .*

Czasami trzeba skonfigurować SignalR klienta używanego przez Blazor Server aplikacje. Na przykład możesz chcieć skonfigurować rejestrowanie na SignalR kliencie, aby zdiagnozować problem z połączeniem.

Aby skonfigurować SignalR klienta w `Pages/_Host.cshtml` pliku:

* Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.
* Wywoływanie `Blazor.start` i przekazywanie obiektu konfiguracji, który określa SignalR Konstruktor.

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

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/logging/index>
