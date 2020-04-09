---
title: Łącze przeglądarki w ASP.NET Core
author: ncarandini
description: W tym artykule wyjaśniono, jak łącze przeglądarki jest funkcją programu Visual Studio, która łączy środowisko programistyczne z co najmniej jedną przeglądarką sieci Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658852"
---
# <a name="browser-link-in-aspnet-core"></a>Łącze przeglądarki w ASP.NET Core

Przez [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)i [Tom Dykstra](https://github.com/tdykstra)

Łącze przeglądarki jest funkcją programu Visual Studio. Tworzy kanał komunikacji między środowiskiem programistycznym a jedną lub kilkoma przeglądarkami internetowymi. Za pomocą łącza przeglądarki można odświeżyć aplikację internetową w kilku przeglądarkach jednocześnie, co jest przydatne do testowania między przeglądarkami.

## <a name="browser-link-setup"></a>Konfiguracja łącza przeglądarki

::: moniker range=">= aspnetcore-3.0"

Dodaj pakiet [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) do projektu. W przypadku ASP.NET podstawowych stron razor lub projektów MVC, należy również włączyć kompilację środowiska <xref:mvc/views/view-compilation>uruchomieniowego plików Razor (*.cshtml*), jak opisano w . Zmiany składni maszynki do golenia są stosowane tylko wtedy, gdy kompilacja środowiska uruchomieniowego została włączona.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Podczas konwertowania projektu ASP.NET Core 2.0 na ASP.NET Core 2.1 i przejścia do [metapakietu Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)zainstaluj pakiet [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) dla funkcji łącza przeglądarki. Szablony projektów ASP.NET Core 2.1 domyślnie używają `Microsoft.AspNetCore.App` metapakiety.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Szablony projektów ASP.NET Core 2.0 **Web Application**, **Empty**i **Web API** używają [metapakiety Microsoft.AspNetCore.All,](xref:fundamentals/metapackage)która zawiera odwołanie do pakietu dla [witryny Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). W związku z `Microsoft.AspNetCore.All` tym za pomocą metapakiety nie wymaga dalszych działań, aby udostępnić łącze przeglądarki do użycia.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Szablon projektu **aplikacji sieci Web** ASP.NET Core 1.x zawiera odwołanie do pakietu [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) Inne typy projektów wymagają dodania `Microsoft.VisualStudio.Web.BrowserLink`odwołania do pakietu .

::: moniker-end

### <a name="configuration"></a>Konfigurowanie

Zadzwoń `UseBrowserLink` w `Startup.Configure` metodzie:

```csharp
app.UseBrowserLink();
```

Wywołanie `UseBrowserLink` jest zazwyczaj umieszczane wewnątrz `if` bloku, który włącza tylko łącze przeglądarki w środowisku deweloperskim. Przykład:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Jak korzystać z łącza przeglądarki

Po otwarciu projektu ASP.NET Core program Visual Studio pokazuje kontrolka paska narzędzi Łącze przeglądarki obok formantu paska narzędzi **Debug target:**

![Menu rozwijane Link przeglądarki](using-browserlink/_static/browserLink-dropdown-menu.png)

Za pomocą paska narzędzi Łącze przeglądarki można:

* Odśwież aplikację internetową w kilku przeglądarkach jednocześnie.
* Otwórz **pulpit nawigacyjny łącza przeglądarki**.
* Włącz lub wyłącz **łącze przeglądarki**. Uwaga: Łącze przeglądarki jest domyślnie wyłączone w programie Visual Studio.
* Włącz lub wyłącz [automatyczną synchronizację CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Odświeżanie aplikacji internetowej w kilku przeglądarkach jednocześnie

Aby wybrać pojedynczą przeglądarkę internetową do uruchomienia podczas uruchamiania projektu, użyj menu rozwijanego w formancie paska narzędzi **Debug target:**

![Menu rozwijane F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Aby otworzyć wiele przeglądarek jednocześnie, wybierz **pozycję Przeglądaj z...** z tego samego rozwijania. Przytrzymaj naciśnięty klawisz <kbd>Ctrl,</kbd> aby zaznaczyć żądane przeglądarki, a następnie kliknij przycisk **Przeglądaj:**

![Otwieranie wielu przeglądarek jednocześnie](using-browserlink/_static/open-many-browsers-at-once.png)

Poniższy zrzut ekranu przedstawia program Visual Studio z otwartym widokiem indeksu i dwiema otwartymi przeglądarkami:

![Przykład synchronizacji z dwiema przeglądarkami](using-browserlink/_static/sync-with-two-browsers-example.png)

Umieść wskaźnik myszy na kontrolce paska narzędzi Łącze przeglądarki, aby wyświetlić przeglądarki połączone z projektem:

![Najedź na końcówkę](using-browserlink/_static/hoover-tip.png)

Zmień widok indeksu, a wszystkie połączone przeglądarki są aktualizowane po kliknięciu przycisku Odświeżanie łącza przeglądarki:

![przeglądarki-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Łącze przeglądarki działa również z przeglądarkami uruchamiane z zewnątrz programu Visual Studio i przejdź do adresu URL aplikacji.

### <a name="the-browser-link-dashboard"></a>Pulpit nawigacyjny łącza przeglądarki

Otwórz okno **Pulpit nawigacyjny łącza przeglądarki** z menu rozwijanego Łącze przeglądarki, aby zarządzać połączeniem z otwartymi przeglądarkami:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Jeśli nie jest połączona żadna przeglądarka, można rozpocząć sesję bez debugowania, wybierając łącze **Wyświetl w przeglądarce:**

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

W przeciwnym razie połączone przeglądarki są wyświetlane ze ścieżką do strony, na którą wyświetla każda przeglądarka:

![browserlink-dashboard-dwa połączenia](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Możesz również kliknąć indywidualną nazwę przeglądarki, aby odświeżyć tylko tę przeglądarkę.

### <a name="enable-or-disable-browser-link"></a>Włączanie lub wyłączanie łącza przeglądarki

Po ponownym włączeniu łącza przeglądarki po jego wyłączeniu należy odświeżyć przeglądarki, aby ponownie je połączyć.

### <a name="enable-or-disable-css-auto-sync"></a>Włączanie lub wyłączanie automatycznej synchronizacji CSS

Gdy css auto-sync jest włączony, połączone przeglądarki są automatycznie odświeżane po wprowadzeniu jakichkolwiek zmian w plikach CSS.

## <a name="how-it-works"></a>Jak to działa

Łącze [SignalR](xref:signalr/introduction) przeglądarki służy do tworzenia kanału komunikacji między programem Visual Studio a przeglądarką. Gdy łącze przeglądarki jest włączone, SignalR program Visual Studio działa jako serwer, z którym może się połączyć wielu klientów (przeglądarki). Łącze przeglądarki rejestruje również składnik oprogramowania pośredniczącego w potoku żądań ASP.NET Core. Ten składnik wstrzykuje odwołania specjalne `<script>` do każdego żądania strony z serwera. Odwołania do skryptów można wyświetlić, wybierając **pozycję Wyświetl źródło** w `<body>` przeglądarce i przewijając do końca zawartości znacznika:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Pliki źródłowe nie są modyfikowane. Składnik oprogramowania pośredniczącego dynamicznie wstrzykuje odwołania do skryptu.

Ponieważ kod po stronie przeglądarki jest cały JavaScript, SignalR działa na wszystkich przeglądarkach, które obsługuje bez konieczności wtyczki przeglądarki.
