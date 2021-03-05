---
title: Blazor SignalR Wskazówki dotyczące ASP.NET Core
author: guardrex
description: Dowiedz się, jak konfigurować połączenia i zarządzać nimi Blazor SignalR .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 63dfd93fbc42a869211bc5cd481a8dbee6eb6c91
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118918"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a>Blazor SignalR Wskazówki dotyczące ASP.NET Core

::: zone pivot="webassembly"

W tym artykule wyjaśniono, jak konfigurować SignalR połączenia w aplikacjach i zarządzać nimi Blazor .

Ogólne wskazówki dotyczące konfiguracji ASP.NET Core SignalR można znaleźć w tematach w <xref:signalr/introduction> obszarze dokumentacji. Aby skonfigurować SignalR [dodanie do Blazor WebAssembly rozwiązania hostowanego](xref:tutorials/signalr-blazor), zobacz <xref:signalr/configuration#configure-server-options> .

## <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR negocjowanie między źródłami na potrzeby uwierzytelniania

Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak cookie nagłówki uwierzytelniania s lub http:

* Służy <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawiania <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> żądań między źródłami [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) .

  `IncludeRequestCredentialsMessageHandler.cs`:

  ```csharp
  using System.Net.Http;
  using System.Threading;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Components.WebAssembly.Http;

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

* Jeśli utworzono połączenie z centrum, przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:

  ```csharp
  HubConnectionBuilder hubConnecton;

  ...

  hubConnecton = new HubConnectionBuilder()
      .WithUrl(new Uri(NavigationManager.ToAbsoluteUri("/chathub")), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

  Poprzedni przykład konfiguruje adres URL połączenia centrum do bezwzględnego adresu URI w `/chathub` , który jest adresem URL używanym w [ SignalR Blazor samouczku with](xref:tutorials/signalr-blazor) w `Index` składniku ( `Pages/Index.razor` ). Identyfikator URI można również ustawić za pośrednictwem ciągu, na przykład `https://signalr.example.com` , lub za pomocą [konfiguracji](xref:blazor/fundamentals/configuration).

Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.

::: moniker range=">= aspnetcore-5.0"

## <a name="render-mode"></a>Tryb renderowania

Jeśli Blazor WebAssembly aplikacja, która korzysta z programu SignalR jest skonfigurowana do PreRender na serwerze, renderowanie wstępne występuje przed nawiązaniem połączenia z serwerem. Aby uzyskać więcej informacji, zobacz następujące artykuły:

* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:signalr/introduction>
* <xref:signalr/configuration>

::: zone-end

::: zone pivot="server"

W tym artykule wyjaśniono, jak konfigurować SignalR połączenia w aplikacjach i zarządzać nimi Blazor .

Ogólne wskazówki dotyczące konfiguracji ASP.NET Core SignalR można znaleźć w tematach w <xref:signalr/introduction> obszarze dokumentacji. Aby skonfigurować SignalR [dodanie do Blazor WebAssembly rozwiązania hostowanego](xref:tutorials/signalr-blazor), zobacz <xref:signalr/configuration#configure-server-options> .

## <a name="circuit-handler-options"></a>Opcje procedury obsługi obwodu

Skonfiguruj Blazor Server obwód z <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> pokazanymi w poniższej tabeli.

| Opcja | Domyślne | Opis |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | Wysyłaj szczegółowe komunikaty o wyjątkach do języka JavaScript, gdy wystąpił nieobsługiwany wyjątek w obwodzie lub gdy metoda .NET nie wywoływana za pomocą kodu JS Interop powoduje wyjątek. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 100 | Maksymalna liczba odłączonych obwodów, które serwer przechowuje w pamięci w danym momencie. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 3 minuty | Maksymalny czas przechowywania połączonego obwodu w pamięci przed jego usunięciem. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 1 minuta | Maksymalny czas oczekiwania serwera przed upływem limitu czasu asynchronicznego wywołania funkcji JavaScript. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 10 | Maksymalna liczba niepotwierdzonych partii renderowania, które serwer przechowuje w pamięci na obwód w danym momencie do obsługi niezawodnego ponownego łączenia. Po osiągnięciu limitu serwer przestaje tworzyć nowe partie renderowania do momentu potwierdzenia, że co najmniej jedna partia zostanie potwierdzona przez klienta. |

Skonfiguruj opcje w programie `Startup.ConfigureServices` przy użyciu opcji delegat <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> . Poniższy przykład przypisuje domyślne wartości opcji pokazanych w powyższej tabeli. Upewnij się, że `Startup.cs` używa <xref:System> przestrzeni nazw ( `using System;` ).

`Startup.ConfigureServices`:

```csharp
services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

Aby skonfigurować <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , użyj <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> z <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> . Aby uzyskać opisy opcji, zobacz <xref:signalr/configuration#configure-server-options> . Poniższy przykład przypisuje domyślne wartości opcji. Upewnij się, że `Startup.cs` używa <xref:System> przestrzeni nazw ( `using System;` ).

`Startup.ConfigureServices`:

```csharp
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

## <a name="reflect-the-connection-state-in-the-ui"></a>Odzwierciedlanie stanu połączenia w interfejsie użytkownika

Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie. Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.

Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stronie.

`Pages/_Host.cshtml`:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Dodaj następujące style CSS do arkusza stylów witryny.

`wwwroot/css/site.css`:

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu przez Blazor platformę.

| Klasa CSS                       | Wskazuje&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Utracono połączenie. Klient próbuje ponownie nawiązać połączenie. Pokaż modalne. |
| `components-reconnect-hide`     | Aktywne połączenie zostanie ponownie nawiązane z serwerem. Ukryj modalne. |
| `components-reconnect-failed`   | Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci. Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` JavaScript. |
| `components-reconnect-rejected` | Odrzucono ponowne połączenie. Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony. Aby ponownie załadować aplikację, zadzwoń do `location.reload()` języka JavaScript. Ten stan połączenia może skutkować tym, że:<ul><li>Wystąpił awaria w obwodzie po stronie serwera.</li><li>Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika. Wystąpienia składników użytkownika są usuwane.</li><li>Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</li></ul> |

## <a name="render-mode"></a>Tryb renderowania

Domyślnie aplikacje uruchamiają Blazor Server interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="initialize-the-blazor-circuit"></a>Inicjowanie Blazor obwodu

Skonfiguruj ręczne uruchamianie Blazor Server [ SignalR obwodu](xref:blazor/hosting-models#circuits) aplikacji w `Pages/_Host.cshtml` pliku:

* Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.
* Umieść skrypt wywołujący `Blazor.start` po `blazor.server.js` tagu skryptu i wewnątrz taga zamykającego `</body>` .

Gdy `autostart` jest wyłączone, dowolny aspekt aplikacji, która nie zależy od obwodu, zwykle działa. Na przykład Routing po stronie klienta działa. Jednak każdy aspekt, który zależy od obwodu, nie działa do momentu `Blazor.start` wywołania. Zachowanie aplikacji jest nieprzewidywalne bez ustalonego obwodu. Na przykład metody komponentów nie są wykonywane podczas odłączania obwodu.

### <a name="initialize-blazor-when-the-document-is-ready"></a>Inicjuj Blazor , gdy dokument jest gotowy

`Pages/_Host.cshtml`:

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

Aby wykonać dodatkowe zadania, takie jak Inicjalizacja międzyoperacyjna JS, użyj [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) do łańcucha z [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) wynikami ręcznego Blazor uruchamiania aplikacji.

`Pages/_Host.cshtml`:

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

### <a name="configure-signalr-client-logging"></a>Konfigurowanie SignalR rejestrowania klientów

W konstruktorze klienta Przekaż `configureSignalR` obiekt konfiguracji, który wywołuje `configureLogging` z poziomu dziennika.

`Pages/_Host.cshtml`:

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

**`onConnectionDown`Należy określić oba elementy i `onConnectionUp` .**

`Pages/_Host.cshtml`:

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

Aby dostosować liczbę ponownych prób ponownego połączenia i interwał, ustaw liczbę ponownych prób ( `maxRetries` ) i okres w milisekundach dozwolony dla każdej próbnej próby ( `retryIntervalMilliseconds` ).

`Pages/_Host.cshtml`:

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

Aby ukryć ekran ponowne połączenie, ustaw dla programu obsługi ponownego połączenia `_reconnectionDisplay` pusty obiekt ( `{}` lub `new Object()` ).

`Pages/_Host.cshtml`:

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

Dostosuj opóźnienie przed wyświetleniem ekranu ponownego połączenia przez ustawienie `transition-delay` właściwości w CSS witryny dla elementu modalnego. W poniższym przykładzie ustawiono opóźnienie przejścia z 500 ms (domyślnie) do 1 000 MS (1 sekunda).

`wwwroot/css/site.css`:

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a>Odłączanie Blazor obwodu od klienta

Domyślnie Blazor obwód jest odłączony, gdy wyzwalane jest [ `unload` zdarzenie strony](https://developer.mozilla.org/docs/Web/API/Window/unload_event) . Aby odłączyć obwód dla innych scenariuszy na kliencie, wywołaj `Blazor.disconnect` w odpowiedniej procedurze obsługi zdarzeń. W poniższym przykładzie obwód jest odłączony, gdy strona jest ukryta ([ `pagehide` zdarzenie](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [Blazor Server zdarzenia ponownego połączenia i zdarzenia cyklu życia składnika](xref:blazor/components/lifecycle#blazor-server-reconnection-events)

::: zone-end
