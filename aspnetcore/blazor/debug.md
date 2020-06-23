---
title: Debuguj ASP.NET Core Blazor Webassembly
author: guardrex
description: Dowiedz się, jak debugować Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 75db5d5e69cb200ebf3bd1dc1e0afed0300214cc
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242774"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Debuguj ASP.NET Core Blazor Webassembly

[Daniel Roth](https://github.com/danroth27)

BlazorAplikacje webassembly można debugować przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome). Alternatywnie możesz debugować aplikację przy użyciu programu Visual Studio lub Visual Studio Code.

Dostępne scenariusze obejmują:

* Ustawianie i usuwanie punktów przerwania.
* Uruchom aplikację z obsługą debugowania w programie Visual Studio i Visual Studio Code (Obsługa<kbd>F5</kbd> ).
* Jeden krok (<kbd>F10</kbd>) za pomocą kodu.
* Wznów wykonywanie kodu za pomocą <kbd>klawisza F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub Visual Studio Code.
* Na ekranie *Ustawienia lokalne* Obserwuj wartości zmiennych lokalnych.
* Zobacz stos wywołań, w tym łańcuchy wywołań, które pochodzą z języka JavaScript do platformy .NET i z programu .NET do języka JavaScript.

Obecnie *nie*można:

* Przerwij w przypadku nieobsłużonych wyjątków.
* Trafij punkty przerwania podczas uruchamiania aplikacji.

Będziemy nadal ulepszać środowisko debugowania w przyszłych wersjach.

## <a name="prerequisites"></a>Wymagania wstępne

Debugowanie wymaga jednej z następujących przeglądarek:

* Microsoft Edge (wersja 80 lub nowsza)
* Google Chrome (wersja 70 lub nowsza)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Włącz debugowanie dla programu Visual Studio i Visual Studio Code

Aby włączyć debugowanie istniejącej Blazor aplikacji webassembly, zaktualizuj `launchSettings.json` plik w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po zaktualizowaniu `launchSettings.json` plik powinien wyglądać podobnie do poniższego przykładu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Właściwość:

* Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor aplikacją webassembly.
* Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.

Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.

## <a name="visual-studio"></a>Visual Studio

Aby debugować Blazor aplikację webassembly w programie Visual Studio:

1. Utwórz nową ASP.NET Core hostowanej Blazor aplikacji sieci webassembly.
1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.
1. Ustaw punkt przerwania w `Pages/Counter.razor` `IncrementCount` metodzie.
1. Przejdź do **`Counter`** karty i wybierz przycisk, aby trafić w punkt przerwania:

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Sprawdź wartość `currentCount` pola w oknie zmiennych lokalnych:

   ![Wyświetl elementy lokalne](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.

Podczas debugowania Blazor aplikacji webassembly można także debugować kod serwera:

1. Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.
1. Przejdź do **`Fetch Data`** karty, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera:

   ![Debuguj dane pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` :

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Naciśnij ponownie klawisz <kbd>F5</kbd> , aby umożliwić kontynuowanie wykonywania, a następnie zapoznaj się z renderowaną tabelą prognozy pogody.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Aby debugować Blazor aplikację webassembly w Visual Studio Code:
 
Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true` .

![Rozszerzenia](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Debuger podglądu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Debuguj autonomiczny Blazor zestaw Webassembly

1. Otwórz autonomiczną Blazor aplikację webassembly w vs Code.

   Jeśli zostanie wyświetlone następujące powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja:
   
   * Upewnij się, że zainstalowano poprawne rozszerzenia.
   * Upewnij się, że debugowanie JavaScript Preview jest włączone.
   * Załaduj ponownie okno.

   ![Wymagana dodatkowa konfiguracja](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Rozpocznij debugowanie przy użyciu skrótu klawiaturowego <kbd>F5</kbd> lub elementu menu.

1. Po wyświetleniu monitu wybierz opcję ** Blazor Debuguj zestaw webassembly** , aby rozpocząć debugowanie.

   ![Lista dostępnych opcji debugowania](index/_static/blazor-vscode-debugtypes.png)

1. Uruchomiona jest aplikacja autonomiczna i zostanie otwarta przeglądarka debugowania.

1. Ustaw punkt przerwania w `IncrementCount` metodzie w `Counter` składniku, a następnie wybierz przycisk, aby trafić w punkt przerwania:

   ![Debuguj licznik w VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Debuguj hostowany Blazor zestaw Webassembly

1. Otwórz hostowaną Blazor aplikację webassembly w vs Code.

1. Jeśli nie ma ustawionej konfiguracji uruchamiania dla projektu, wyświetlane jest następujące powiadomienie. Wybierz pozycję **Tak**.

   ![Dodaj wymagane zasoby](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. W oknie Wybór wybierz projekt *serwera* w ramach hostowanego rozwiązania.

`launch.json`Generowany jest plik z konfiguracją uruchamiania do uruchamiania debugera.

### <a name="attach-to-an-existing-debugging-session"></a>Dołącz do istniejącej sesji debugowania

Aby dołączyć do uruchomionej Blazor aplikacji, Utwórz `launch.json` plik z następującą konfiguracją:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Dołączanie do sesji debugowania jest obsługiwane tylko w przypadku aplikacji autonomicznych. Aby można było używać debugowania pełnego stosu, należy uruchomić aplikację z VS Code.

### <a name="launch-configuration-options"></a>Opcje konfiguracji uruchamiania

Dla typu debugowania są obsługiwane następujące opcje konfiguracji uruchamiania `blazorwasm` .

| Opcja    | Opis |
| --------- | ----------- |
| `request` | Służy `launch` do uruchamiania i dołączania sesji debugowania do Blazor aplikacji webassembly lub `attach` do dołączania sesji debugowania do już uruchomionej aplikacji. |
| `url`     | Adres URL, który ma zostać otwarty w przeglądarce podczas debugowania. Wartość domyślna to `https://localhost:5001` . |
| `browser` | Przeglądarka do uruchomienia dla sesji debugowania. Ustaw `edge` lub `chrome`. Wartość domyślna to `chrome` . |
| `trace`   | Służy do generowania dzienników z debugera JS. Ustaw, aby `true` generować dzienniki. |
| `hosted`  | Musi mieć ustawioną wartość w `true` przypadku uruchamiania i debugowania hostowanej Blazor aplikacji sieci webassembly. |
| `webRoot` | Określa ścieżkę bezwzględną serwera sieci Web. Należy ustawić, jeśli aplikacja jest obsługiwana z podmarszruty. |
| `timeout` | Liczba milisekund oczekiwania na dołączenie sesji debugowania. Wartość domyślna to 30 000 milisekund (30 sekund). |
| `program` | Odwołanie do pliku wykonywalnego, aby uruchomić serwer hostowanej aplikacji. Musi być ustawiona, jeśli `hosted` ma wartość `true` . |
| `cwd`     | Katalog roboczy, w którym jest uruchomiona aplikacja. Musi być ustawiona, jeśli `hosted` ma wartość `true` . |
| `env`     | Zmienne środowiskowe do zapewnienia uruchomionego procesu. Ma zastosowanie tylko wtedy `hosted` , gdy jest ustawiona na `true` . |

### <a name="example-launch-configurations"></a>Przykładowe konfiguracje uruchamiania

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>Uruchamianie i debugowanie autonomicznej Blazor aplikacji sieci Webassembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Dołącz do uruchomionej aplikacji pod określonym adresem URL

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Uruchamianie i debugowanie hostowanej Blazor aplikacji sieci Webassembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Debuguj w przeglądarce

1. Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.

1. Naciśnij klawisze <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>.

1. Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym. Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** . Strona błędu zawiera instrukcje dotyczące uruchamiania przeglądarki z otwartym portem debugowania, dzięki czemu Blazor serwer proxy debugowania może połączyć się z aplikacją. *Zamknij wszystkie wystąpienia przeglądarki* i uruchom ponownie przeglądarkę zgodnie z instrukcjami.

Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania otwiera nową kartę debugera. Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET w aplikacji. Rozwiń każdy zestaw i Znajdź `.cs` / `.razor` pliki źródłowe dostępne do debugowania. Ustaw punkty przerwania, przełącz się z powrotem do karty aplikacji, a punkty przerwania są trafień, gdy kod jest wykonywany. Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.

Blazorudostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci. Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy. Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).

## <a name="browser-source-maps"></a>Mapy źródeł przeglądarki

Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta. Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM. Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli występują błędy, następujące porady mogą pomóc:

* Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce. W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.
* Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
