---
title: Debuguj ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak debugować Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: b7e246c20bf12f8ddf07cff54864836cb535aa60
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340000"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Debuguj ASP.NET Core Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssembly aplikacje mogą być debugowane przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome). Możesz również debugować aplikację przy użyciu następujących zintegrowanych środowisk projektowych (środowisk IDE):

* Visual Studio
* Visual Studio dla komputerów Mac
* Visual Studio Code

Dostępne scenariusze obejmują:

* Ustawianie i usuwanie punktów przerwania.
* Uruchom aplikację z obsługą debugowania w środowisk IDE.
* Pojedynczy krok po kodzie.
* Wznów wykonywanie kodu za pomocą skrótu klawiaturowego w środowisk IDE.
* W oknie *lokalne* Sprawdź wartości zmiennych lokalnych.
* Zobacz stos wywołań, w tym łańcuchy wywołań między językami JavaScript i .NET.

Obecnie *nie* można:

* Przerwij w przypadku nieobsłużonych wyjątków.
* Punkty przerwania trafień podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania. Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

Debugowanie wymaga jednej z następujących przeglądarek:

* Google Chrome (wersja 70 lub nowsza) (domyślnie)
* Microsoft Edge (wersja 80 lub nowsza)

Upewnij się, że zapory lub serwery proxy nie blokują komunikacji z serwerem proxy debugowania ( `NodeJS` proces). Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja zapory](#firewall-configuration) .

Visual Studio dla komputerów Mac wymaga wersji 8,8 (kompilacja 1532) lub nowszej:

1. Zainstaluj najnowszą wersję Visual Studio dla komputerów Mac, wybierając przycisk **pobierz Visual Studio dla komputerów Mac** w [firmie Microsoft: Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).
1. Wybierz kanał *podglądu* z programu Visual Studio. Aby uzyskać więcej informacji, zobacz [Instalowanie wersji zapoznawczej Visual Studio dla komputerów Mac](/visualstudio/mac/install-preview).

> [!NOTE]
> Przeglądarka Apple Safari w systemie macOS nie jest obecnie obsługiwana.

## <a name="enable-debugging"></a>Włączanie debugowania

Aby włączyć debugowanie dla istniejącej Blazor WebAssembly aplikacji, zaktualizuj `launchSettings.json` plik w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po zaktualizowaniu `launchSettings.json` plik powinien wyglądać podobnie do poniższego przykładu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Właściwość:

* Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor WebAssembly aplikacją.
* Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.

Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Aby debugować Blazor WebAssembly aplikację w programie Visual Studio:

1. Utwórz nową aplikację hostowaną ASP.NET Core Blazor WebAssembly .
1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.

   > [!NOTE]
   > **Uruchamianie bez debugowania** ( <kbd>Ctrl</kbd> + <kbd>F5</kbd>) nie jest obsługiwane. Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.

1. W `*Client*` aplikacji ustaw punkt przerwania w `currentCount++;` wierszu `Pages/Counter.razor` .
1. W przeglądarce przejdź do `Counter` strony i wybierz przycisk **kliknij** , aby trafić punkt przerwania.
1. W programie Visual Studio Sprawdź wartość `currentCount` pola w oknie **zmiennych lokalnych** .
1. Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.

Podczas debugowania Blazor WebAssembly aplikacji można także debugować kod serwera:

1. Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.
1. Przejdź do `Fetch Data` strony, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera.
1. Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` .
1. Naciśnij ponownie klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, i zobacz tabelę prognoz pogody renderowanych w przeglądarce.

> [!NOTE]
> Punkty przerwania **nie** są trafiania podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania. Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.

Jeśli aplikacja jest hostowana pod inną [ścieżką bazową aplikacji](xref:blazor/host-and-deploy/index#app-base-path) niż `/` , zaktualizuj następujące właściwości w programie w `Properties/launchSettings.json` celu odzwierciedlenia podstawowej ścieżki aplikacji:

* `applicationUrl`:

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* `inspectUri` dla każdego profilu:

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

Symbole zastępcze w poprzednich ustawieniach:

* `{INSECURE PORT}`: Niezabezpieczony port. Wartość losowa jest domyślnie określona, ale jest dozwolony port niestandardowy.
* `{APP BASE PATH}`: Ścieżka podstawowa aplikacji.
* `{SECURE PORT}`: Bezpieczny port. Wartość losowa jest domyślnie określona, ale jest dozwolony port niestandardowy.
* `{PROFILE 1, 2, ... N}`: Uruchamianie profilów ustawień. Zazwyczaj aplikacja określa domyślnie więcej niż jeden profil (na przykład profil IIS Express i profil projektu, który jest używany przez serwer Kestrel).

W poniższych przykładach aplikacja jest hostowana `/OAT` z ścieżką bazową aplikacji skonfigurowaną w programie `wwwroot/index.html` jako `<base href="/OAT/">` :

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

Aby uzyskać informacje na temat używania niestandardowej ścieżki bazy aplikacji dla Blazor WebAssembly aplikacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path> .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<h2 id="vscode">Debuguj autonomiczne Blazor WebAssembly</h2>

1. Otwórz aplikację autonomiczną Blazor WebAssembly w vs Code.

   Użytkownik może otrzymać powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja:

   > Do debugowania aplikacji jest wymagana dodatkowa konfiguracja Blazor WebAssembly .

   Jeśli otrzymasz powiadomienie:

   * Upewnij się, że zainstalowano najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) . Aby sprawdzić zainstalowane rozszerzenia, Otwórz rozszerzenia **widoku**  >  **Extensions** na pasku menu lub wybierz ikonę **rozszerzenia** na pasku bocznym **działania** .
   * Upewnij się, że debugowanie JavaScript Preview jest włączone. Otwórz ustawienia na pasku menu ( **File**  >  **Preferences**  >  **Ustawienia** preferencji plików). Wyszukaj przy użyciu słów kluczowych `debug preview` . Upewnij się, że w wynikach wyszukiwania znajduje się pole wyboru **debuguj > JavaScript: Użyj opcji Podgląd** jest zaznaczone. Jeśli opcja Włącz debugowanie podglądu nie istnieje, Uaktualnij do najnowszej wersji VS Code lub zainstaluj [rozszerzenie debugera JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (vs Code w wersji 1,46 lub starszej).
   * Załaduj ponownie okno.

1. Rozpocznij debugowanie przy użyciu skrótu klawiaturowego <kbd>F5</kbd> lub elementu menu.

   > [!NOTE]
   > **Uruchamianie bez debugowania** ( <kbd>Ctrl</kbd> + <kbd>F5</kbd>) nie jest obsługiwane. Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.

1. Po wyświetleniu monitu wybierz opcję **Blazor WebAssembly Debuguj** , aby rozpocząć debugowanie.

1. Uruchomiona jest aplikacja autonomiczna i zostanie otwarta przeglądarka debugowania.

1. W `*Client*` aplikacji ustaw punkt przerwania w `currentCount++;` wierszu `Pages/Counter.razor` .

1. W przeglądarce przejdź do `Counter` strony i wybierz przycisk **kliknij** , aby trafić punkt przerwania.

> [!NOTE]
> Punkty przerwania **nie** są trafiania podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania. Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.

## <a name="debug-hosted-no-locblazor-webassembly"></a>Debuguj hostowane Blazor WebAssembly

1. Otwórz Blazor WebAssembly folder rozwiązania aplikacji hostowanej w vs Code.

1. Jeśli nie ma ustawionej konfiguracji uruchamiania dla projektu, wyświetlane jest następujące powiadomienie. Wybierz pozycję **Tak**.

   > Brak wymaganych zasobów do skompilowania i debugowania w "{APPLICATION NAME}". Dodać je?

1. W palecie poleceń w górnej części okna wybierz projekt *serwera* w ramach rozwiązania hostowanego.

`launch.json`Generowany jest plik z konfiguracją uruchamiania do uruchamiania debugera.

## <a name="attach-to-an-existing-debugging-session"></a>Dołącz do istniejącej sesji debugowania

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

## <a name="launch-configuration-options"></a>Opcje konfiguracji uruchamiania

Następujące opcje konfiguracji uruchamiania są obsługiwane dla `blazorwasm` typu debugowania ( `.vscode/launch.json` ).

| Opcja    | Opis |
| --------- | ----------- |
| `request` | Służy `launch` do uruchamiania i dołączania sesji debugowania do Blazor WebAssembly aplikacji lub `attach` do dołączania sesji debugowania do już uruchomionej aplikacji. |
| `url`     | Adres URL, który ma zostać otwarty w przeglądarce podczas debugowania. Wartość domyślna to `https://localhost:5001` . |
| `browser` | Przeglądarka do uruchomienia dla sesji debugowania. Ustaw `edge` lub `chrome`. Wartość domyślna to `chrome` . |
| `trace`   | Służy do generowania dzienników z debugera JS. Ustaw, aby `true` generować dzienniki. |
| `hosted`  | Musi mieć ustawioną wartość w `true` przypadku uruchamiania i debugowania hostowanej Blazor WebAssembly aplikacji. |
| `webRoot` | Określa ścieżkę bezwzględną serwera sieci Web. Należy ustawić, jeśli aplikacja jest obsługiwana z podmarszruty. |
| `timeout` | Liczba milisekund oczekiwania na dołączenie sesji debugowania. Wartość domyślna to 30 000 milisekund (30 sekund). |
| `program` | Odwołanie do pliku wykonywalnego, aby uruchomić serwer hostowanej aplikacji. Musi być ustawiona, jeśli `hosted` ma wartość `true` . |
| `cwd`     | Katalog roboczy, w którym jest uruchomiona aplikacja. Musi być ustawiona, jeśli `hosted` ma wartość `true` . |
| `env`     | Zmienne środowiskowe do zapewnienia uruchomionego procesu. Ma zastosowanie tylko wtedy `hosted` , gdy jest ustawiona na `true` . |

## <a name="example-launch-configurations"></a>Przykładowe konfiguracje uruchamiania

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Uruchamianie i debugowanie aplikacji autonomicznej Blazor WebAssembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>Dołącz do uruchomionej aplikacji pod określonym adresem URL

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Uruchamianie i debugowanie hostowanej Blazor WebAssembly aplikacji przy użyciu przeglądarki Microsoft Edge

Domyślna konfiguracja przeglądarki w przeglądarce Google Chrome. W przypadku korzystania z przeglądarki Microsoft Edge na potrzeby debugowania ustaw wartość `browser` `edge` . Aby użyć programu Google Chrome, nie ustawiaj `browser` opcji lub ustaw wartość opcji na `chrome` .

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

W poprzednim przykładzie `MyHostedApp.Server.dll` jest to zestaw aplikacji *serwera* . Folder znajduje się `.vscode` w folderze rozwiązania obok `Client` `Server` folderów, i `Shared` .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Aby debugować Blazor WebAssembly aplikację w Visual Studio dla komputerów Mac:

1. Utwórz nową aplikację hostowaną ASP.NET Core Blazor WebAssembly .
1. Naciśnij <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby uruchomić aplikację w debugerze.

   > [!NOTE]
   > **Uruchamianie bez debugowania** ( <kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) nie jest obsługiwane. Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.

   > [!IMPORTANT]
   > Przeglądarka Google Chrome lub Microsoft Edge musi być wybraną przeglądarką dla sesji debugowania.

1. W `*Client*` aplikacji ustaw punkt przerwania w `currentCount++;` wierszu `Pages/Counter.razor` .
1. W przeglądarce przejdź do `Counter` strony i wybierz przycisk **kliknij** , aby trafić w punkt przerwania:
1. W programie Visual Studio Sprawdź wartość `currentCount` pola w oknie **zmiennych lokalnych** .
1. Naciśnij <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby kontynuować wykonywanie.

Podczas debugowania Blazor WebAssembly aplikacji można także debugować kod serwera:

1. Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.
1. Przejdź do `Fetch Data` strony, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera.
1. Naciśnij <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` .
1. Naciśnij <kbd>&#8984;</kbd> + ponownie przycisk <kbd>&#8617;</kbd>&#8984;, aby umożliwić kontynuowanie wykonywania, i zobacz tabelę prognoz pogody renderowanych w przeglądarce.

> [!NOTE]
> Punkty przerwania **nie** są trafiania podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania. Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.

Aby uzyskać więcej informacji, zobacz [debugowanie za pomocą Visual Studio dla komputerów Mac](/visualstudio/mac/debugging).

---

## <a name="debug-in-the-browser"></a>Debuguj w przeglądarce

*Wskazówki zawarte w tej sekcji dotyczą przeglądarki Google Chrome i przeglądarki Microsoft Edge działającej w systemie Windows.*

1. Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.

1. Uruchom przeglądarkę i przejdź do adresu URL aplikacji (na przykład `https://localhost:5001` ).

1. W przeglądarce spróbuj rozpocząć debugowanie zdalne, naciskając klawisz <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>d</kbd>.

   Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym, które nie jest ustawieniem domyślnym. Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** z instrukcjami dotyczącymi uruchamiania przeglądarki z otwartym portem debugowania. Postępuj zgodnie z instrukcjami wyświetlanymi w przeglądarce, otwierając nowe okno przeglądarki. Zamknij poprzednie okno przeglądarki.

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania w poprzednim kroku otwiera nową kartę debugera.

1. Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET aplikacji znajdujących się w `file://` węźle.

1. W kodzie składnika ( `.razor` pliki) i pliki kodu C# ( `.cs` ) punkty przerwania, które można ustawić, są trafień, gdy zostanie wykonany kod. Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.

Blazor udostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci. Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy. Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).

## <a name="browser-source-maps"></a>Mapy źródeł przeglądarki

Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta. Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM. Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.

## <a name="firewall-configuration"></a>Konfiguracja zapory

Jeśli zapora blokuje komunikację z serwerem proxy debugowania, należy utworzyć regułę wyjątku zapory, która zezwala na komunikację między przeglądarką a `NodeJS` procesem.

> [!WARNING]
> Aby uniknąć tworzenia vulnerablities zabezpieczeń, należy wprowadzić modyfikację konfiguracji zapory. Starannie stosuj wskazówki dotyczące zabezpieczeń, stosuj najlepsze rozwiązania w zakresie zabezpieczeń oraz ostrzeżenia wydawane przez producenta zapory.
>
> Zezwalanie na otwieranie komunikacji z `NodeJS` procesem:
>
> * Otwiera serwer węzła do dowolnego połączenia, w zależności od możliwości i konfiguracji zapory.
> * Może być ryzykowne w zależności od sieci.
> * **Jest zalecane tylko na maszynach deweloperskich.**
>
> Jeśli to możliwe, zezwalanie na tylko otwartej komunikacji z `NodeJS` procesem **w sieciach zaufanych lub prywatnych**.

Aby uzyskać wskazówki dotyczące konfiguracji [zapory systemu Windows](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) , zobacz [Tworzenie reguły programu w programie lub usłudze przychodzącej](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule). Aby uzyskać więcej informacji, zobacz [Zapora Windows Defender z zabezpieczeniami zaawansowanymi](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) i pokrewne artykuły w zestawie dokumentacji zapory systemu Windows.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli występują błędy, następujące porady mogą pomóc:

* Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce. W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.
* Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Program Visual Studio wymaga opcji **Enable JavaScript Debug for ASP.NET (Chrome, Edge i IE)** w **Tools**  >  **opcji** narzędzia  >  **debugowanie**  >  **Ogólne**. Jest to ustawienie domyślne dla programu Visual Studio. Jeśli debugowanie nie działa, upewnij się, że opcja jest zaznaczona.
* Jeśli w środowisku używany jest serwer proxy HTTP, upewnij się, że `localhost` jest on uwzględniony w ustawieniach obejścia serwera proxy. Można to zrobić, ustawiając `NO_PROXY` zmienną środowiskową w jednej z:
  * `launchSettings.json`Plik dla projektu.
  * Na poziomie zmiennych środowiskowych użytkownika lub systemu na potrzeby zastosowania do wszystkich aplikacji. W przypadku korzystania ze zmiennej środowiskowej Uruchom ponownie program Visual Studio, aby zmiany zaczęły obowiązywać.
* Upewnij się, że zapory lub serwery proxy nie blokują komunikacji z serwerem proxy debugowania ( `NodeJS` proces). Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja zapory](#firewall-configuration) .

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>Punkty przerwania w elemencie `OnInitialized{Async}` nie trafią

BlazorSerwer proxy debugowania struktury zajmuje trochę czasu, dlatego punkty przerwania w [ `OnInitialized{Async}` metodzie cyklu życia](xref:blazor/components/lifecycle#component-initialization-methods) mogą nie zostać trafione. Zalecamy dodanie opóźnienia na początku treści metody, aby dać serwerowi debugowania jakiś czas na uruchomienie przed trafieniem punktu przerwania. Możesz uwzględnić opóźnienie na podstawie [ `if` dyrektywy kompilatora](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) , aby upewnić się, że opóźnienie nie jest obecne dla kompilacji wydania aplikacji.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>Limit czasu programu Visual Studio (Windows)

Jeśli program Visual Studio zgłasza wyjątek, że uruchomienie karty debugowania nie powiodło się z informacją o osiągnięciu limitu czasu, można dostosować limit czasu przy użyciu ustawienia rejestru:

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

`{TIMEOUT}`Symbol zastępczy w poprzednim poleceniu jest w milisekundach. Na przykład jedna minuta jest przypisana jako `60000` .
