---
title: 'Debuguj ASP.NET Core:::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Dowiedz się, jak debugować :::no-loc(Blazor)::: aplikacje.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/debug
ms.openlocfilehash: 14943b9f7847ac9144addfdf16a003f6fc8c340c
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159707"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Debuguj ASP.NET Core:::no-loc(Blazor WebAssembly):::

[Daniel Roth](https://github.com/danroth27)

:::no-loc(Blazor WebAssembly):::aplikacje mogą być debugowane przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome). Alternatywnie możesz debugować aplikację przy użyciu programu Visual Studio lub Visual Studio Code.

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

* Google Chrome (wersja 70 lub nowsza) (domyślnie)
* Microsoft Edge (wersja 80 lub nowsza)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Włącz debugowanie dla programu Visual Studio i Visual Studio Code

Aby włączyć debugowanie dla istniejącej :::no-loc(Blazor WebAssembly)::: aplikacji, zaktualizuj `launchSettings.json` plik w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po zaktualizowaniu `launchSettings.json` plik powinien wyglądać podobnie do poniższego przykładu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Właściwość:

* Umożliwia środowisku IDE wykrywanie, czy aplikacja jest :::no-loc(Blazor WebAssembly)::: aplikacją.
* Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez :::no-loc(Blazor)::: serwer proxy debugowania.

Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.

## <a name="visual-studio"></a>Visual Studio

Aby debugować :::no-loc(Blazor WebAssembly)::: aplikację w programie Visual Studio:

1. Utwórz nową aplikację hostowaną ASP.NET Core :::no-loc(Blazor WebAssembly)::: .
1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.
1. Ustaw punkt przerwania w `Pages/Counter.razor` `IncrementCount` metodzie.
1. Przejdź do **`Counter`** karty i wybierz przycisk, aby trafić w punkt przerwania:

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Sprawdź wartość `currentCount` pola w oknie zmiennych lokalnych:

   ![Wyświetl elementy lokalne](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.

Podczas debugowania :::no-loc(Blazor WebAssembly)::: aplikacji można także debugować kod serwera:

1. Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.
1. Przejdź do **`Fetch Data`** karty, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera:

   ![Debuguj dane pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` :

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Naciśnij ponownie klawisz <kbd>F5</kbd> , aby umożliwić kontynuowanie wykonywania, a następnie zapoznaj się z renderowaną tabelą prognozy pogody.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

### <a name="debug-standalone-no-locblazor-webassembly"></a>Debuguj autonomiczne:::no-loc(Blazor WebAssembly):::

1. Otwórz aplikację autonomiczną :::no-loc(Blazor WebAssembly)::: w vs Code.

   Jeśli zostanie wyświetlone następujące powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja:
   
   * Upewnij się, że zainstalowano poprawne rozszerzenia.
   * Upewnij się, że debugowanie JavaScript Preview jest włączone.
   * Załaduj ponownie okno.

   ![Wymagana dodatkowa konfiguracja](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Rozpocznij debugowanie przy użyciu skrótu klawiaturowego <kbd>F5</kbd> lub elementu menu.

1. Po wyświetleniu monitu wybierz opcję ** :::no-loc(Blazor WebAssembly)::: Debuguj** , aby rozpocząć debugowanie.

   ![Lista dostępnych opcji debugowania](index/_static/blazor-vscode-debugtypes.png)

1. Uruchomiona jest aplikacja autonomiczna i zostanie otwarta przeglądarka debugowania.

1. Ustaw punkt przerwania w `IncrementCount` metodzie w `Counter` składniku, a następnie wybierz przycisk, aby trafić w punkt przerwania:

   ![Debuguj licznik w VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a>Debuguj hostowane:::no-loc(Blazor WebAssembly):::

1. Otwórz :::no-loc(Blazor WebAssembly)::: folder rozwiązania aplikacji hostowanej w vs Code.

1. Jeśli nie ma ustawionej konfiguracji uruchamiania dla projektu, wyświetlane jest następujące powiadomienie. Wybierz pozycję **Tak**.

   ![Dodaj wymagane zasoby](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. W palecie poleceń w górnej części okna wybierz projekt *serwera* w ramach rozwiązania hostowanego.

`launch.json`Generowany jest plik z konfiguracją uruchamiania do uruchamiania debugera.

### <a name="attach-to-an-existing-debugging-session"></a>Dołącz do istniejącej sesji debugowania

Aby dołączyć do uruchomionej :::no-loc(Blazor)::: aplikacji, Utwórz `launch.json` plik z następującą konfiguracją:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing :::no-loc(Blazor WebAssembly)::: Application"
}
```

> [!NOTE]
> Dołączanie do sesji debugowania jest obsługiwane tylko w przypadku aplikacji autonomicznych. Aby można było używać debugowania pełnego stosu, należy uruchomić aplikację z VS Code.

### <a name="launch-configuration-options"></a>Opcje konfiguracji uruchamiania

Następujące opcje konfiguracji uruchamiania są obsługiwane dla `blazorwasm` typu debugowania ( `.vscode/launch.json` ).

| Opcja    | Opis |
| --------- | ----------- |
| `request` | Służy `launch` do uruchamiania i dołączania sesji debugowania do :::no-loc(Blazor WebAssembly)::: aplikacji lub `attach` do dołączania sesji debugowania do już uruchomionej aplikacji. |
| `url`     | Adres URL, który ma zostać otwarty w przeglądarce podczas debugowania. Wartość domyślna to `https://localhost:5001` . |
| `browser` | Przeglądarka do uruchomienia dla sesji debugowania. Ustaw `edge` lub `chrome`. Wartość domyślna to `chrome` . |
| `trace`   | Służy do generowania dzienników z debugera JS. Ustaw, aby `true` generować dzienniki. |
| `hosted`  | Musi mieć ustawioną wartość w `true` przypadku uruchamiania i debugowania hostowanej :::no-loc(Blazor WebAssembly)::: aplikacji. |
| `webRoot` | Określa ścieżkę bezwzględną serwera sieci Web. Należy ustawić, jeśli aplikacja jest obsługiwana z podmarszruty. |
| `timeout` | Liczba milisekund oczekiwania na dołączenie sesji debugowania. Wartość domyślna to 30 000 milisekund (30 sekund). |
| `program` | Odwołanie do pliku wykonywalnego, aby uruchomić serwer hostowanej aplikacji. Musi być ustawiona, jeśli `hosted` ma wartość `true` . |
| `cwd`     | Katalog roboczy, w którym jest uruchomiona aplikacja. Musi być ustawiona, jeśli `hosted` ma wartość `true` . |
| `env`     | Zmienne środowiskowe do zapewnienia uruchomionego procesu. Ma zastosowanie tylko wtedy `hosted` , gdy jest ustawiona na `true` . |

### <a name="example-launch-configurations"></a>Przykładowe konfiguracje uruchamiania

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Uruchamianie i debugowanie aplikacji autonomicznej :::no-loc(Blazor WebAssembly):::

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

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Uruchamianie i debugowanie hostowanej :::no-loc(Blazor WebAssembly)::: aplikacji przy użyciu przeglądarki Microsoft Edge

Domyślna konfiguracja przeglądarki w przeglądarce Google Chrome. W przypadku korzystania z przeglądarki Microsoft Edge na potrzeby debugowania ustaw wartość `browser` `edge` . Aby użyć programu Google Chrome, nie ustawiaj `browser` opcji lub ustaw wartość opcji na `chrome` .

```json
{
  "name": "Launch and Debug Hosted :::no-loc(Blazor WebAssembly)::: App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

W poprzednim przykładzie `MyHostedApp.Server.dll` jest to zestaw aplikacji *serwera* . Folder znajduje się `.vscode` w folderze rozwiązania obok `Client` `Server` folderów, i `Shared` .

## <a name="debug-in-the-browser"></a>Debuguj w przeglądarce

1. Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.

1. Uruchom przeglądarkę i przejdź do adresu URL aplikacji (na przykład `https://localhost:5001` ).

1. W przeglądarce spróbuj rozpocząć debugowanie zdalne, naciskając klawisz <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>.

   Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym, które nie jest ustawieniem domyślnym. Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** z instrukcjami dotyczącymi uruchamiania przeglądarki z otwartym portem debugowania. Postępuj zgodnie z instrukcjami wyświetlanymi w przeglądarce, otwierając nowe okno przeglądarki. Zamknij poprzednie okno przeglądarki.

1. Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania (<kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>) otwiera nową kartę debugera.

1. Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET aplikacji znajdujących się w `file://` węźle.

1. W kodzie składnika ( `.razor` pliki) i pliki kodu C# ( `.cs` ) punkty przerwania, które można ustawić, są trafień, gdy zostanie wykonany kod. Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.

:::no-loc(Blazor):::udostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci. Gdy skrót klawiaturowy debugowania zostanie nacionięty, :::no-loc(Blazor)::: wskazuje, że program Chrome devtools na serwerze proxy. Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).

## <a name="browser-source-maps"></a>Mapy źródeł przeglądarki

Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta. Jednak :::no-loc(Blazor)::: obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM. Zamiast tego program :::no-loc(Blazor)::: wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli występują błędy, następujące porady mogą pomóc:

* Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce. W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.
* Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Program Visual Studio wymaga opcji **Enable JavaScript Debug for ASP.NET (Chrome, Edge i IE)** w **Tools**  >  **opcji**narzędzia  >  **debugowanie**  >  **Ogólne**. Jest to ustawienie domyślne dla programu Visual Studio. Jeśli debugowanie nie działa, upewnij się, że opcja jest zaznaczona.
