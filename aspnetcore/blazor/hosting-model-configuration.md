---
title: Konfiguracja modelu Blazor hostingu ASP.NET Core
author: guardrex
description: Dowiedz Blazor się więcej o konfiguracji modelu hostingu, w tym o tym, jak zintegrować komponenty Razor ze stronami Razor i aplikacjami MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306433"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>konfiguracja modelu hostingowego ASP.NET Core Blazor

Przez [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

W tym artykule omówiono konfigurację modelu hostingu.

## <a name="blazor-webassembly"></a>Zestaw WebAssembly Blazor

W wersji ASP.NET Core 3.2 Preview 3, Blazor WebAssembly obsługuje konfigurację z:

* *wwwroot/appsettings.json*
* *wwwroot/appsettings. {ŚRODOWISKO}.json*

W aplikacji Blazor Hosted [środowisko wykonawcze](xref:fundamentals/environments) jest takie samo jak wartość aplikacji serwera.

Podczas uruchamiania aplikacji lokalnie środowiska domyślnie rozwoju. Po opublikowaniu aplikacji środowisko domyślnie ma wartość Produkcja. Aby uzyskać więcej informacji, w tym <xref:fundamentals/environments>jak skonfigurować środowisko, zobacz .

> [!WARNING]
> Konfiguracja w aplikacji Blazor WebAssembly jest widoczna dla użytkowników. **Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**

Pliki konfiguracyjne są buforowane do użytku w trybie offline. Dzięki [progresywnym aplikacjom sieci Web (PWA)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracyjne podczas tworzenia nowego wdrożenia. Edytowanie plików konfiguracyjnych między wdrożeniami nie ma wpływu, ponieważ:

* Użytkownicy mają buforowane wersje plików, które nadal używają.
* Pliki *service-worker.js* i *service-worker-assets.js* muszą zostać przebudowane na kompilacji, która sygnalizuje aplikacji przy następnej wizycie online użytkownika, że aplikacja została ponownie rozmieszczona.

Aby uzyskać więcej informacji na temat sposobu obsługi <xref:blazor/progressive-web-app#background-updates>aktualizacji w tle przez programy PWA, zobacz .

## <a name="blazor-server"></a>Serwer Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Odzwierciedlanie stanu połączenia w interfejsie użytkownika

Gdy klient wykryje, że połączenie zostało utracone, domyślny interfejs użytkownika jest wyświetlany użytkownikowi, podczas gdy klient próbuje ponownie połączyć. Jeśli ponowne połączenie nie powiedzie się, użytkownik ma możliwość ponowienia próby.

Aby dostosować interfejs użytkownika, zdefiniuj element z `id` dokączą `components-reconnect-modal` `<body>` na stronie *_Host.cshtml* Razor:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

W poniższej tabeli opisano klasy `components-reconnect-modal` CSS zastosowane do elementu.

| Klasa CSS                       | Wskazuje&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Utracone połączenie. Klient próbuje ponownie nawiązać połączenie. Pokaż modal. |
| `components-reconnect-hide`     | Aktywne połączenie zostanie ponownie nawiązane do serwera. Ukryj modal. |
| `components-reconnect-failed`   | Ponowne połączenie nie powiodło się, prawdopodobnie z powodu awarii sieci. Aby spróbować ponownie połączyć, zadzwoń do `window.Blazor.reconnect()`pliku . |
| `components-reconnect-rejected` | Ponowne połączenie odrzucone. Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze zostanie utracony. Aby ponownie załadować `location.reload()`aplikację, zadzwoń . Ten stan połączenia może spowodować, gdy:<ul><li>Wystąpi awaria obwodu po stronie serwera.</li><li>Klient jest rozłączany wystarczająco długo, aby serwer porzucić stan użytkownika. Wystąpienia składników, z którymi użytkownik wchodzi w interakcję, są usuwane.</li><li>Serwer zostanie ponownie uruchomiony lub proces roboczy aplikacji jest odtwoniany.</li></ul> |

### <a name="render-mode"></a>Tryb renderowania

Aplikacje Blazor Server są domyślnie skonfigurowane do prerender interfejsu użytkownika na serwerze przed nawiązaniem połączenia klienta z serwerem. Jest to skonfigurowane na stronie *_Host.cshtml* Razor:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`określa, czy składnik:

* Jest prerendered do strony.
* Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.

| `RenderMode`        | Opis |
| ------------------- | ----------- |
| `ServerPrerendered` | Renderuje składnik do statycznego kodu HTML Blazor i zawiera znacznik aplikacji serwera. Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji. |
| `Server`            | Renderuje znacznik aplikacji Blazor serwera. Dane wyjściowe ze składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji. |
| `Static`            | Renderuje składnik do statycznego kodu HTML. |

Renderowanie składników serwera ze statycznej strony HTML nie jest obsługiwane.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Renderowanie stanowych składników interaktywnych ze stron i widoków Razor

Stanowe składniki interaktywne można dodać do strony lub widoku Razor.

Gdy strona lub widok renderuje:

* Składnik jest prerendered ze stroną lub widoku.
* Początkowy stan składnika używany do wstępnego rozsyłania jest tracony.
* Nowy stan składnika SignalR jest tworzony po nawiązaniu połączenia.

Następująca strona Razor `Counter` renderuje składnik:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Renderowanie składników nieinteraktywnych ze stron i widoków razor

Na następującej stronie Razor `Counter` składnik jest statycznie renderowany z wartością początkową określoną przy użyciu formularza:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Ponieważ `MyComponent` jest statycznie renderowane, składnik nie może być interaktywny.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Konfigurowanie SignalR klienta Blazor dla aplikacji serwera

Czasami należy skonfigurować SignalR klienta używanego Blazor przez aplikacje serwera. Na przykład można skonfigurować rejestrowanie na SignalR kliencie, aby zdiagnozować problem z połączeniem.

Aby skonfigurować SignalR klienta w pliku *Pages/_Host.cshtml:*

* Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.
* Wywołaj `Blazor.start` i przekaż w obiekcie SignalR konfiguracji, który określa konstruktora.

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
