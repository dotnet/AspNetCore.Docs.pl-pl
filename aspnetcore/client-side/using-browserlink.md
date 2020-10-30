---
title: Link przeglądarki w ASP.NET Core
author: ncarandini
description: Wyjaśnia, w jaki sposób łącze przeglądarki jest funkcją programu Visual Studio, która łączy środowisko programistyczne z co najmniej jedną przeglądarką sieci Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054492"
---
# <a name="browser-link-in-aspnet-core"></a>Link przeglądarki w ASP.NET Core

Autorzy [Nicolò Carandini](https://github.com/ncarandini), [Jan Wasson](https://github.com/MikeWasson)i [Tomasz Dykstra](https://github.com/tdykstra)

Link przeglądarki to funkcja programu Visual Studio. Tworzy kanał komunikacyjny między środowiskiem deweloperskim i jedną lub wieloma przeglądarkami sieci Web. Możesz użyć linku przeglądarki, aby odświeżyć aplikację sieci Web w kilku przeglądarkach jednocześnie, co jest przydatne w przypadku testowania między przeglądarkami.

## <a name="browser-link-setup"></a>Konfiguracja linku przeglądarki

::: moniker range=">= aspnetcore-3.0"

Dodaj pakiet [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) do projektu. W przypadku Razor stron ASP.NET Core lub projektów MVC należy również włączyć kompilację Razor plików ( *. cshtml* ) środowiska uruchomieniowego zgodnie z opisem w temacie <xref:mvc/views/view-compilation> . Razor zmiany składni są stosowane tylko wtedy, gdy Kompilacja środowiska uruchomieniowego została włączona.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Podczas konwertowania projektu ASP.NET Core 2,0 na ASP.NET Core 2,1 i przejścia do [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), zainstaluj pakiet [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) dla funkcji linku do przeglądarki. Szablony projektów ASP.NET Core 2,1 korzystają domyślnie z `Microsoft.AspNetCore.App` pakietu.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Szablony projektu **aplikacji sieci Web** , **pustej** i **internetowego interfejsu API** ASP.NET Core 2,0 używają [pakietu Microsoft. AspNetCore. All](xref:fundamentals/metapackage), który zawiera odwołanie do pakietu dla [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). W związku z tym używanie `Microsoft.AspNetCore.All` pakietu nie wymaga żadnych dalszych działań w celu udostępnienia linku przeglądarki do użycia.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Szablon projektu **aplikacji sieci Web** ASP.NET Core 1. x zawiera odwołanie do pakietu dla pakietu [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) . Inne typy projektów wymagają dodania odwołania do pakietu do `Microsoft.VisualStudio.Web.BrowserLink` .

::: moniker-end

### <a name="configuration"></a>Konfiguracja

Wywołanie `UseBrowserLink` `Startup.Configure` metody:

```csharp
app.UseBrowserLink();
```

`UseBrowserLink`Wywołanie jest zwykle umieszczane wewnątrz `if` bloku, który umożliwia tylko łącze przeglądarki w środowisku deweloperskim. Przykład:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Jak używać linku przeglądarki

Gdy masz otwarty projekt ASP.NET Core, program Visual Studio wyświetli kontrolkę pasek narzędzi linku przeglądarki obok kontrolki paska narzędzi **elementu docelowego debugowania** :

![Menu rozwijane linku przeglądarki](using-browserlink/_static/browserLink-dropdown-menu.png)

Za pomocą kontrolki paska narzędzi łącza przeglądarki można:

* Odśwież aplikację sieci Web w kilku przeglądarkach jednocześnie.
* Otwórz **pulpit nawigacyjny linku do przeglądarki** .
* Włącza lub wyłącza **link przeglądarki** . Uwaga: łącze przeglądarki jest domyślnie wyłączone w programie Visual Studio.
* Włączać lub wyłączać funkcję [autosynchronizacji CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Odśwież aplikację sieci Web w kilku przeglądarkach jednocześnie

Aby wybrać jedną przeglądarkę sieci Web, która ma być uruchamiana podczas uruchamiania projektu, użyj menu rozwijanego w formancie paska narzędzi **elementu docelowego debugowania** :

![Menu rozwijane F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Aby otworzyć wiele przeglądarek jednocześnie, wybierz pozycję **Przeglądaj za pomocą...** z tego samego listy rozwijanej. Naciśnij i przytrzymaj klawisz <kbd>Ctrl</kbd> , aby wybrać przeglądarki, a następnie kliknij przycisk **Przeglądaj** :

![Otwórz wiele przeglądarek jednocześnie](using-browserlink/_static/open-many-browsers-at-once.png)

Poniższy zrzut ekranu przedstawia program Visual Studio z otwartym widokiem indeksu i dwiema otwartymi przeglądarkami:

![Przykład synchronizacji z dwoma przeglądarkami](using-browserlink/_static/sync-with-two-browsers-example.png)

Umieść kursor nad kontrolką paska narzędzi łącza przeglądarki, aby wyświetlić przeglądarki, które są połączone z projektem:

![Wskazówki dotyczące aktywowania](using-browserlink/_static/hoover-tip.png)

Zmień widok indeksu, a wszystkie połączone przeglądarki są aktualizowane po kliknięciu przycisku Odśwież linku do przeglądarki:

![przeglądarki — synchronizacja ze zmianami](using-browserlink/_static/browsers-sync-to-changes.png)

Łącze przeglądarki współpracuje również z przeglądarkami uruchamianymi spoza programu Visual Studio i przejdź do adresu URL aplikacji.

### <a name="the-browser-link-dashboard"></a>Pulpit nawigacyjny linków przeglądarki

Otwórz okno **pulpit nawigacyjny link do przeglądarki** z menu rozwijanego linku do przeglądarki, aby zarządzać połączeniem przy użyciu otwartych przeglądarek:

![Open-browserslink — pulpit nawigacyjny](using-browserlink/_static/open-browserlink-dashboard.png)

Jeśli żadna przeglądarka nie jest połączona, możesz uruchomić sesję niedebugowania, wybierając **Widok w przeglądarce** link:

![browserlink — pulpit nawigacyjny — brak połączeń](using-browserlink/_static/browserlink-dashboard-no-connections.png)

W przeciwnym razie połączone przeglądarki są wyświetlane ze ścieżką do strony, którą pokazuje Każda przeglądarka:

![browserlink — pulpit nawigacyjny — dwa połączenia](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Możesz również kliknąć nazwę konkretnej przeglądarki w celu odświeżenia tylko tej przeglądarki.

### <a name="enable-or-disable-browser-link"></a>Włącz lub Wyłącz link przeglądarki

Po ponownym włączeniu linku przeglądarki po jego wyłączeniu należy odświeżyć przeglądarki w celu ponownego nawiązania połączenia.

### <a name="enable-or-disable-css-auto-sync"></a>Włączanie lub wyłączanie autosynchronizacji CSS

Gdy automatyczna synchronizacja CSS jest włączona, podłączane przeglądarki są automatycznie odświeżane po wprowadzeniu jakichkolwiek zmian w plikach CSS.

## <a name="how-it-works"></a>Jak to działa

Link do przeglądarki używa [SignalR](xref:signalr/introduction) do tworzenia kanału komunikacyjnego między programem Visual Studio i przeglądarką. Gdy łącze przeglądarki jest włączone, program Visual Studio działa jako SignalR serwer, z którym mogą się łączyć wielu klientów (przeglądarki). Link przeglądarki rejestruje również składnik pośredniczący w potoku żądania ASP.NET Core. Ten składnik `<script>` wprowadza odwołania specjalne do każdego żądania strony z serwera. Odwołania do skryptu można zobaczyć, wybierając opcję **Wyświetl źródło** w przeglądarce i przewijając do końca `<body>` zawartości tagu:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Pliki źródłowe nie są modyfikowane. Składnik pośredniczący wprowadza odwołania do skryptów dynamicznie.

Ponieważ kod po stronie przeglądarki to wszystkie skrypty JavaScript, działa on we wszystkich przeglądarkach, które SignalR obsługują nie wymaga wtyczki przeglądarki.
