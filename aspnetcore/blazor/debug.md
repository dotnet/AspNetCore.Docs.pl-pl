---
title: Debuguj ASP.NET Core Blazor webassembly
author: guardrex
description: Dowiedz się, Blazor jak debugować aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 9acbb8e7b122a8d527d16ce33af01c2e7e7608bf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767541"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Debuguj ASP.NET Core Blazor webassembly

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorAplikacje webassembly można debugować przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome).  Alternatywnie możesz debugować aplikację przy użyciu programu Visual Studio lub Visual Studio Code.

Dostępne scenariusze obejmują:

* Ustawianie i usuwanie punktów przerwania.
* Uruchom aplikację z obsługą debugowania w programie Visual Studio i Visual Studio Code (Obsługa<kbd>F5</kbd> ).
* Jeden krok (<kbd>F10</kbd>) za pomocą kodu.
* Wznów wykonywanie kodu za pomocą <kbd>klawisza F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub Visual Studio Code.
* Na ekranie *Ustawienia lokalne* Obserwuj wartości zmiennych lokalnych.
* Zobacz stos wywołań, w tym łańcuchy wywołań, które pochodzą z języka JavaScript do platformy .NET i z programu .NET do języka JavaScript.

Obecnie *nie*można:

* Inspekcja tablic.
* Umieść wskaźnik myszy, aby sprawdzić członków.
* Wkrocz debugowanie do lub z kodu zarządzanego.
* Pełna obsługa inspekcji typów wartości.
* Przerwij w przypadku nieobsłużonych wyjątków.
* Trafij punkty przerwania podczas uruchamiania aplikacji.
* Debugowanie aplikacji za pomocą procesu roboczego usługi.

Będziemy nadal ulepszać środowisko debugowania w przyszłych wersjach.

## <a name="prerequisites"></a>Wymagania wstępne

Debugowanie wymaga jednej z następujących przeglądarek:

* Microsoft Edge (wersja 80 lub nowsza)
* Google Chrome (wersja 70 lub nowsza)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Włącz debugowanie dla programu Visual Studio i Visual Studio Code

Debugowanie jest włączane automatycznie dla nowych projektów, które są tworzone przy użyciu szablonu projektu ASP.NET Core 3,2 Blazor w wersji zapoznawczej 3 lub nowszej ([bieżąca wersja to 3,2 wersja zapoznawcza 4](xref:blazor/get-started)).

Aby włączyć debugowanie istniejącej Blazor aplikacji webassembly, zaktualizuj plik *profilu launchsettings. JSON* w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po zaktualizowaniu plik *profilu launchsettings. JSON* powinien wyglądać podobnie do poniższego przykładu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri` Właściwość:

* Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor aplikacją webassembly.
* Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką Blazorprzez serwer proxy debugowania.

## <a name="visual-studio"></a>Visual Studio

Aby debugować Blazor aplikację webassembly w programie Visual Studio:

1. Upewnij się [, że zainstalowano najnowszą wersję zapoznawczą programu Visual Studio 2019 16,6](https://visualstudio.com/preview) (wersja zapoznawcza 2 lub nowsza).
1. Utwórz nową ASP.NET Core hostowanej Blazor aplikacji sieci webassembly.
1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.
1. Ustaw punkt przerwania w elemencie *Counter. Razor* w `IncrementCount` metodzie.
1. Przejdź do karty **licznik** i wybierz przycisk, aby trafić w punkt przerwania:

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Sprawdź wartość `currentCount` pola w oknie zmiennych lokalnych:

   ![Wyświetl elementy lokalne](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.

Podczas debugowania Blazor aplikacji webassembly można także debugować kod serwera:

1. Ustaw punkt przerwania na stronie *FetchData. Razor* w `OnInitializedAsync`temacie.
1. Ustaw punkt przerwania w `WeatherForecastController` metodzie `Get` Action.
1. Przejdź do karty **pobieranie danych** , aby trafić pierwszy punkt przerwania `FetchData` w składniku tuż przed wysłaniem żądania HTTP do serwera:

   ![Debuguj dane pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na `WeatherForecastController`serwerze w:

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Naciśnij ponownie klawisz <kbd>F5</kbd> , aby umożliwić kontynuowanie wykonywania, a następnie zapoznaj się z renderowaną tabelą prognozy pogody.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Aby debugować Blazor aplikację webassembly w Visual Studio Code:
 
1. Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na. `true`

   ![Rozszerzenia](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Debuger podglądu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Otwórz istniejącą Blazor aplikację webassembly z włączonym debugowaniem.

   * Jeśli otrzymujesz następujące powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja, upewnij się, że są zainstalowane odpowiednie rozszerzenia i włączono debugowanie JavaScript w wersji zapoznawczej, a następnie załaduj ponownie okno:

     ![Dodatkowa konfiguracja wymagane](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Powiadomienia umożliwiają dodanie wymaganych zasobów do aplikacji na potrzeby kompilowania i debugowania. Wybierz opcję **tak**:

     ![Dodaj wymagane zasoby](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Uruchamianie aplikacji w debugerze jest procesem dwuetapowym:

   1 \. **Najpierw**Uruchom aplikację przy użyciu konfiguracji uruchamiania **uruchamiania programu .NET CoreBlazor (Standalone)** .

   2 \. **Po uruchomieniu aplikacji**Uruchom przeglądarkę przy użyciu **zestawu .NET Core Debug Blazor Web Assembly w** konfiguracji uruchamiania programu Chrome (wymaga programu Chrome). Aby użyć `type` krawędzi zamiast przeglądarki Chrome, Zmień konfigurację uruchamiania w pliku *. programu vscode/Launch. JSON* z `pwa-chrome` na. `pwa-msedge`

1. Ustaw punkt przerwania w `IncrementCount` metodzie w `Counter` składniku, a następnie wybierz przycisk, aby trafić w punkt przerwania:

   ![Debuguj licznik w VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Debuguj w przeglądarce

1. Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.

1. Naciśnij klawisze <kbd>SHIFT</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.

1. Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym. Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** . Strona błędu zawiera instrukcje dotyczące uruchamiania przeglądarki z otwartym portem debugowania, dzięki czemu serwer Blazor proxy debugowania może połączyć się z aplikacją. *Zamknij wszystkie wystąpienia przeglądarki* i uruchom ponownie przeglądarkę zgodnie z instrukcjami.

Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania otwiera nową kartę debugera. Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET w aplikacji. Rozwiń każdy zestaw i Znajdź pliki źródłowe *CS*/*. Razor* dostępne do debugowania. Ustaw punkty przerwania, przełącz się z powrotem do karty aplikacji, a punkty przerwania są trafień, gdy kod jest wykonywany. Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.

Blazorudostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci. Gdy skrót klawiaturowy debugowania zostanie nacionięty Blazor , wskazuje, że program Chrome devtools na serwerze proxy. Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).

## <a name="browser-source-maps"></a>Mapy źródeł przeglądarki

Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta. Jednak obecnie Blazor nie mapuje języka C# bezpośrednio do języka JavaScript/WASM. Zamiast tego Blazor program wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli występują błędy, Poniższa Wskazówka może pomóc:

Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce. W konsoli programu wykonaj `localStorage.clear()` polecenie, aby usunąć wszystkie punkty przerwania.
