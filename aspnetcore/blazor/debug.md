---
title: Debugowanie ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, Blazor jak debugować aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 7273ae3d240de0b59a58069fdcc1880247379751
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661608"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a>Debugowanie ASP.NET Core Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorAplikacje WebAssembly można debugować za pomocą narzędzi do tworzenia przeglądarki w przeglądarkach opartych na Chromium (Edge/Chrome).  Alternatywnie można debugować aplikację za pomocą programu Visual Studio lub Visual Studio Code.

Dostępne scenariusze obejmują:

* Ustawianie i usuwanie punktów przerwania.
* Uruchom aplikację z obsługą debugowania w programie Visual Studio i visual studio code (obsługa<kbd>F5).</kbd>
* Jednoetapowy (<kbd>F10</kbd>) za pośrednictwem kodu.
* Wznów wykonanie kodu za pomocą <kbd>F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub visual studio code.
* Na wyświetlaczu *Locals* należy obserwować wartości zmiennych lokalnych.
* Zobacz stos wywołań, w tym łańcuchy wywołań, które przechodzą z języka JavaScript do platformy .NET i z platformy .NET do JavaScript.

Na razie *nie można:*

* Sprawdź tablice.
* Najedź kursorem, aby sprawdzić członków.
* Krok debugowania do lub z kodu zarządzanego.
* Mieć pełną obsługę sprawdzania typów wartości.
* Przerwij na nieobsługiwał wyjątki.
* Trafić punkty przerwania podczas uruchamiania aplikacji.
* Debugowanie aplikacji za pomocą procesu roboczego usługi.

Firma We will continue to improve the debugging experience in upcoming releases.We will continue to improve the debugging experience in upcoming releases.

## <a name="prerequisites"></a>Wymagania wstępne

Debugowanie wymaga jednej z następujących przeglądarek:

* Microsoft Edge (wersja 80 lub nowsza)
* Google Chrome (wersja 70 lub nowsza)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Włączanie debugowania dla programu Visual Studio i kodu programu Visual Studio

Debugowanie jest włączane automatycznie dla nowych projektów, które są tworzone przy użyciu ASP.NET Core Blazor 3.2 Preview 3 lub nowszym szablonie projektu WebAssembly[(bieżąca wersja to 3.2 Preview 4](xref:blazor/get-started)).

Aby włączyć debugowanie Blazor dla istniejącej aplikacji WebAssembly, zaktualizuj plik *launchSettings.json* w projekcie startowym, aby uwzględnić następującą `inspectUri` właściwość w każdym profilu uruchamiania:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po zaktualizowaniu plik *launchSettings.json* powinien wyglądać podobnie do następującego przykładu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Obiekt: `inspectUri`

* Umożliwia IDE wykryć, że Blazor aplikacja jest webassembly aplikacji.
* Nakazuje infrastruktury debugowania skryptu, aby Blazorpołączyć się z przeglądarką za pośrednictwem debugowania serwera proxy.

## <a name="visual-studio"></a>Visual Studio

Aby debugować aplikację Blazor WebAssembly w programie Visual Studio:

1. Upewnij się, że [zainstalowano najnowszą wersję zapoznawczą programu Visual Studio 2019 16.6](https://visualstudio.com/preview) (wersja zapoznawcza 2 lub nowsza).
1. Utwórz nową ASP.NET Core hostowane Blazor WebAssembly aplikacji.
1. Naciśnij <kbd>klawisz F5,</kbd> aby uruchomić aplikację w debugerze.
1. Ustaw punkt przerwania w *Counter.brzytwa* w metodzie. `IncrementCount`
1. Przejdź do karty **Licznik** i wybierz przycisk, aby trafić w punkt przerwania:

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Sprawdź wartość pola `currentCount` w oknie dla mieszkańców:

   ![Zobacz lokalnych mieszkańców](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Naciśnij <kbd>klawisz F5,</kbd> aby kontynuować wykonywanie.

Podczas debugowania Blazor aplikacji WebAssembly można również debugować kod serwera:

1. Ustaw punkt przerwania na stronie *FetchData.brzytwa* w `OnInitializedAsync`programie .
1. Ustaw punkt przerwania `WeatherForecastController` w `Get` metodzie akcji.
1. Przejdź do karty **Pobierz dane,** aby trafić `FetchData` pierwszy punkt przerwania w składniku tuż przed wysuniem żądania HTTP do serwera:

   ![Debugowanie danych pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Naciśnij <kbd>klawisz F5,</kbd> aby kontynuować wykonywanie, a `WeatherForecastController`następnie naciśnij punkt przerwania na serwerze w :

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Naciśnij klawisz <kbd>F5</kbd> ponownie, aby umożliwić kontynuowanie wykonywania i wyświetlić tabelę prognozy pogody renderowane.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Aby debugować aplikację Blazor WebAssembly w programie Visual Studio Code:
 
1. Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i rozszerzenie [debugera JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true`.

   ![Rozszerzenia](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Debuger podglądu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Otwórz istniejącą Blazor aplikację WebAssembly z włączonym debugowaniem.

   * Jeśli zostanie wyświetlone następujące powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja, upewnij się, że masz zainstalowane poprawne rozszerzenia i włączone debugowanie w wersji zapoznawczej JavaScript, a następnie ponownie załaduj okno:

     ![Dodatkowa konfiguracja requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Powiadomienie oferuje dodać wymagane zasoby do aplikacji do tworzenia i debugowania. Wybierz **tak**:

     ![Dodawanie wymaganych zasobów](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Uruchamianie aplikacji w debugerze jest procesem dwuetapowym:

   1\. **Najpierw**uruchom aplikację przy użyciu konfiguracji **uruchamiania .NET Core Launch (Standalone).Blazor **

   2\. **Po uruchomieniu aplikacji**uruchom przeglądarkę za pomocą **zestawu Blazor .NET Core Debug Web Assembly w** konfiguracji uruchamiania Chrome (wymaga Chrome). Aby użyć edge'a zamiast `type` Chrome, zmień konfigurację uruchamiania w pliku `pwa-chrome` `pwa-msedge` *.vscode/launch.json* z na .

1. Ustaw punkt przerwania `IncrementCount` w `Counter` metodzie w komponencie, a następnie wybierz przycisk, aby trafić punkt przerwania:

   ![Licznik debugowania w kodzie VS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Debugowanie w przeglądarce

1. Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.

1. Naciśnij <kbd>klawisz Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.

1. Przeglądarka musi być uruchamiana z włączonym debugowaniem zdalnym. Jeśli debugowanie zdalne jest wyłączone, zostanie wygenerowana strona błędu **karty przeglądarki nie można znaleźć debugowania.** Strona błędu zawiera instrukcje dotyczące uruchamiania przeglądarki z otwartym Blazor portem debugowania, aby serwer proxy debugowania mógł połączyć się z aplikacją. *Zamknij wszystkie wystąpienia przeglądarki* i uruchom ponownie przeglądarkę zgodnie z instrukcjami.

Gdy przeglądarka jest uruchomiona z włączonym debugowaniem zdalnym, skrót klawiaturowy debugowania otwiera nową kartę debugera. Po chwili na karcie **Źródła** jest wyświetlana lista zestawów .NET w aplikacji. Rozwiń każdy zestaw i znajdź pliki źródłowe *.cs*/*.razor* dostępne do debugowania. Ustaw punkty przerwania, przełącz się z powrotem do karty aplikacji, a punkty przerwania zostaną trafione podczas wykonywania kodu. Po trafieniu punktu przerwania, jednoetapowe<kbd>(F10)</kbd>za pośrednictwem kodu lub wznowić<kbd>(F8</kbd>) wykonanie kodu normalnie.

Blazorzapewnia debugowanie serwera proxy, który implementuje [protokół Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół o . informacje specyficzne dla sieci. Po naciśnięciu skrótu klawiaturowego debugowania Blazor wskazuje mapę Chrome DevTools na serwerze proxy. Serwer proxy łączy się z oknem przeglądarki, które chcesz debugować (stąd konieczność włączenia zdalnego debugowania).

## <a name="browser-source-maps"></a>Mapy źródeł przeglądarki

Mapy źródłowe przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są powszechnie używane do debugowania po stronie klienta. Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM. Zamiast Blazor tego, czy interpretacja IL w przeglądarce, więc mapy źródłowe nie są istotne.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli nadajesz się na błędy, może pomóc następująca wskazówka:

Na karcie **Debuger** otwórz narzędzia programistyczne w przeglądarce. W konsoli wykonaj, `localStorage.clear()` aby usunąć wszystkie punkty przerwania.
