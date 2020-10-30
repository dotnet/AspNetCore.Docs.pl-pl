---
title: Korzystanie z usług JavaScript do tworzenia aplikacji jednostronicowych w ASP.NET Core
author: scottaddie
description: Dowiedz się więcej na temat korzyści z używania usług JavaScript do tworzenia aplikacji jednostronicowej (SPA) obsługiwanej przez ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
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
uid: client-side/spa-services
ms.openlocfilehash: 1b9f5b4b4e066cdd3dd5fbfa666c7a087949979f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054648"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Korzystanie z usług JavaScript do tworzenia aplikacji jednostronicowych w ASP.NET Core

Przez [Scott Addie](https://github.com/scottaddie) i [Fiyaz Hasan](https://fiyazhasan.me/)

Aplikacja jednostronicowa (SPA) to popularny typ aplikacji sieci Web ze względu na jego zaawansowaną obsługę. Integrowanie struktur lub bibliotek SPA po stronie klienta, takich jak [kątowy](https://angular.io/) lub [reaguje](https://facebook.github.io/react/), ze strukturami po stronie serwera, takimi jak ASP.NET Core, może być trudne. Usługi JavaScript opracowano w celu zmniejszenia liczby problemów w procesie integracji. Umożliwia bezproblemowe działanie między różnymi stosami technologii klienta i serwera.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Funkcje opisane w tym artykule są przestarzałe w odniesieniu do ASP.NET Core 3,0. W pakiecie NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) jest dostępny prostszy mechanizm integracji systemu Spa Framework. Aby uzyskać więcej informacji, zobacz [[anons] Obsoleting Microsoft. AspNetCore. SpaServices i Microsoft. AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Co to jest usługa JavaScript

Usługi JavaScript to zbiór technologii po stronie klienta dla ASP.NET Core. Celem jest umieszczenie ASP.NET Core jako preferowanej platformy serwerowej dla deweloperów na potrzeby tworzenia aplikacji jednostronicowych.

Usługi JavaScript składają się z dwóch różnych pakietów NuGet:

* [Microsoft. AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Te pakiety są przydatne w następujących scenariuszach:

* Uruchamianie kodu JavaScript na serwerze
* Używanie struktury SPA lub biblioteki
* Kompilowanie zasobów po stronie klienta za pomocą pakietu WebPack

Większość ostrości tego artykułu została umieszczona przy użyciu pakietu SpaServices.

## <a name="what-is-spaservices"></a>Co to jest SpaServices

SpaServices został utworzony, aby pomieścić ASP.NET Core jako preferowana platforma po stronie serwera dla deweloperów do tworzenia aplikacji jednostronicowych. SpaServices nie jest wymagana do opracowania aplikacji jednostronicowych z ASP.NET Core i nie blokuje deweloperów w konkretnym środowisku klienta.

SpaServices zapewnia przydatną infrastrukturę, taką jak:

* [Renderowanie wstępne po stronie serwera](#server-side-prerendering)
* [Oprogramowanie pośredniczące dla deweloperów pakietu WebPack](#webpack-dev-middleware)
* [Zastępowanie modułu gorącego](#hot-module-replacement)
* [Pomocnicy routingu](#routing-helpers)

Zbiorowo te składniki infrastruktury rozszerzają zarówno przepływ pracy deweloperskiej, jak i środowisko uruchomieniowe. Składniki można przyjmować pojedynczo.

## <a name="prerequisites-for-using-spaservices"></a>Wymagania wstępne dotyczące używania SpaServices

Aby korzystać z SpaServices, Zainstaluj następujące elementy:

* [Node.js](https://nodejs.org/) (wersja 6 lub nowsza) z npm

  * Aby sprawdzić, czy te składniki są zainstalowane i można je znaleźć, uruchom następujące polecenie w wierszu polecenia:

    ```console
    node -v && npm -v
    ```

  * W przypadku wdrażania w witrynie sieci Web systemu Azure żadna akcja nie jest wymagana &mdash;Node.js jest zainstalowana i dostępna w środowiskach serwerów.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * W systemie Windows przy użyciu programu Visual Studio 2017 zestaw SDK jest instalowany przez wybranie obciążenia **międzyplatformowego dla aplikacji .NET Core** .

* Pakiet NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)

## <a name="server-side-prerendering"></a>Renderowanie wstępne po stronie serwera

Aplikacja uniwersalna (znana również jako isomorphic) jest aplikacją języka JavaScript, która może działać zarówno na serwerze, jak i na komputerze klienckim. Kątowy, reagowanie i inne popularne struktury zapewniają uniwersalną platformę dla tego stylu tworzenia aplikacji. Pomysłem jest, aby najpierw renderować składniki struktury na serwerze za pośrednictwem Node.js, a następnie delegować dalsze wykonywanie do klienta.

ASP.NET Core [pomocników tagów](xref:mvc/views/tag-helpers/intro) zapewnianych przez SpaServices upraszczają implementację wstępnego renderowania po stronie serwera przez wywoływanie funkcji języka JavaScript na serwerze.

### <a name="server-side-prerendering-prerequisites"></a>Wymagania wstępne dotyczące renderowania po stronie serwera

Zainstaluj pakiet npm [renderowania z obsługą ASPNET](https://www.npmjs.com/package/aspnet-prerendering) :

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Konfiguracja renderowania wstępnego po stronie serwera

Pomocników tagów są odnajdywani za pomocą rejestracji przestrzeni nazw w pliku *_ViewImports. cshtml* projektu:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Te pomocniki tagów dezłożonegoją komunikację bezpośrednio z interfejsami API niskiego poziomu, wykorzystując składnię przypominającą kod HTML w Razor widoku:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>skrypt ASP-PreRender-module tagów modułu

`asp-prerender-module`Pomocnik tagu, użyty w poprzednim przykładzie kodu, wykonuje *ClientApp/dist/main-server.js* na serwerze za pośrednictwem Node.js. W celu zapewnienia przejrzystości plik *main-server.js* jest artefaktem zadania Transpilation języka TypeScript-to-JavaScript w procesie kompilacji [pakietu WebPack](https://webpack.github.io/) . Pakiet WebPack definiuje alias punktu wejścia, `main-server` a następnie przechodzenie wykresu zależności dla tego aliasu rozpoczyna się od pliku *ClientApp/Boot-Server. TS* :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

W poniższym przykładzie kątowym plik *ClientApp/Boot-Server. TS* wykorzystuje `createServerRenderer` funkcję i `RenderResult` Typ `aspnet-prerendering` pakietu npm do konfigurowania renderowania serwera za pośrednictwem Node.js. Znaczniki HTML przeznaczone do renderowania po stronie serwera są przesyłane do wywołania funkcji rozpoznawania, które jest opakowane w obiekt JavaScript o jednoznacznie określonym typie `Promise` . `Promise`Istotność obiektu polega na tym, że asynchronicznie dostarcza znacznik HTML do strony w celu iniekcji w elemencie symbolu zastępczego modelu DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>ASP-PreRender — tag danych

W połączeniu z `asp-prerender-module` pomocnikiem tagów, `asp-prerender-data` pomocnika tagów może służyć do przekazywania informacji kontekstowych z Razor widoku do kodu JavaScript po stronie serwera. Na przykład następujące znaczniki przekazują dane użytkownika do `main-server` modułu:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Otrzymany `UserName` argument jest serializowany przy użyciu wbudowanego serializatora JSON i jest przechowywany w `params.data` obiekcie. W poniższym przykładzie kątowym dane są używane do konstruowania spersonalizowanego powitania w obrębie `h1` elementu:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Nazwy właściwości przesyłane przez pomocników tagów są reprezentowane przy użyciu notacji **PascalCase** . Przeciwieństwo do języka JavaScript, gdzie te same nazwy właściwości są reprezentowane przez **CamelCase** . Domyślna konfiguracja serializacji JSON jest odpowiedzialna za tę różnicę.

Aby rozwijać poprzedni przykład kodu, dane mogą być przekazywane z serwera do widoku przez Hydrating `globals` Właściwości dostarczonej do `resolve` funkcji:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

`postList`Tablica zdefiniowana wewnątrz `globals` obiektu jest dołączona do obiektu globalnego przeglądarki `window` . Ta zmienna podnoszenia do zakresu globalnego eliminuje duplikowanie nakładów pracy, szczególnie w odniesieniu do ładowania tych samych danych na serwerze i ponownie na kliencie.

![globalna zmienna postList dołączona do obiektu window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Oprogramowanie pośredniczące dla deweloperów pakietu WebPack

[Oprogramowanie pośredniczące dla deweloperów pakietu WebPack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) wprowadza Ulepszony przepływ pracy programistycznej, dzięki czemu pakiet WebPack kompiluje zasoby na żądanie. Oprogramowanie pośredniczące automatycznie kompiluje i obsługuje zasoby po stronie klienta po ponownym załadowaniu strony w przeglądarce. Alternatywnym podejściem jest ręczne wywoływanie elementu WebPack za pośrednictwem skryptu kompilacji npm projektu, gdy zostanie zmieniona zależność innej firmy lub kod niestandardowy. Skrypt kompilacji npm w pliku *package.json* jest przedstawiony w poniższym przykładzie:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Wymagania wstępne dotyczące oprogramowania pośredniczącego WebPack dev

Zainstaluj pakiet npm [ASPNET-WebPack](https://www.npmjs.com/package/aspnet-webpack) :

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Konfiguracja oprogramowania pośredniczącego WebPack dev

Oprogramowanie pośredniczące programu WebPack dla deweloperów jest rejestrowane w potoku żądania HTTP przez następujący kod *Startup.cs* w `Configure` metodzie pliku Startup.cs:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

`UseWebpackDevMiddleware`Metoda rozszerzenia musi być wywoływana przed [zarejestrowaniem hostingu pliku statycznego](xref:fundamentals/static-files) za pośrednictwem `UseStaticFiles` metody rozszerzenia. Ze względów bezpieczeństwa należy zarejestrować oprogramowanie pośredniczące tylko wtedy, gdy aplikacja działa w trybie programistycznym.

Właściwość pliku *webpack.config.js* `output.publicPath` nakazuje oprogramowaniu pośredniczącemu oglądanie zmian w `dist` folderze:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Zastępowanie modułu gorącego

Zastanów się nad funkcją [zastępowania modułu](https://webpack.js.org/concepts/hot-module-replacement/) internetowego (HMR) elementu WebPack jako ewolucją [oprogramowania pośredniczącego dla deweloperów pakietu WebPack](#webpack-dev-middleware). HMR wprowadza wszystkie te same korzyści, ale dodatkowo usprawnia przepływ pracy programistycznej przez automatyczne aktualizowanie zawartości strony po skompilowaniu zmian. Nie należy mylić tego programu z odświeżaniem przeglądarki, co może zakłócać bieżący stan w pamięci i sesję debugowania SPA. Istnieje link na żywo między usługą oprogramowania pośredniczącego aplikacji WebPack a przeglądarką, co oznacza, że zmiany są przekazywane do przeglądarki.

### <a name="hot-module-replacement-prerequisites"></a>Wymagania wstępne dotyczące zastępowania modułu aktywnego

Zainstaluj pakiet [WebPack-gorąca-Middle](https://www.npmjs.com/package/webpack-hot-middleware) npm Pack:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Konfiguracja wymiany modułu aktywnego

Składnik HMR musi być zarejestrowany w potoku żądania HTTP MVC w `Configure` metodzie:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Podobnie jak w przypadku [oprogramowania pośredniczącego w pakiecie WebPack](#webpack-dev-middleware), `UseWebpackDevMiddleware` Metoda rozszerzenia musi być wywoływana przed `UseStaticFiles` metodą rozszerzenia. Ze względów bezpieczeństwa należy zarejestrować oprogramowanie pośredniczące tylko wtedy, gdy aplikacja działa w trybie programistycznym.

Plik *webpack.config.js* musi definiować `plugins` tablicę, nawet jeśli jest pusta:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Po załadowaniu aplikacji w przeglądarce karta konsolowa narzędzia deweloperskie zawiera potwierdzenie aktywacji HMR:

![Komunikat połączony z zastępowaniem aktywnego modułu](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Pomocnicy routingu

W większości aplikacji jednostronicowych opartych na ASP.NET Core, routing po stronie klienta jest często pożądany oprócz routingu po stronie serwera. Systemy routingu SPA i MVC mogą działać niezależnie bez zakłóceń. Istnieje jednak jeden przypadek graniczny proponujący wyzwania: Identyfikowanie odpowiedzi HTTP 404.

Rozważmy scenariusz, w którym jest używana trasa bezrozszerzająca `/some/page` . Załóżmy, że żądanie nie jest zgodne ze wzorcem po stronie serwera, ale jego wzorzec jest zgodny z trasą po stronie klienta. Teraz Rozważmy żądanie przychodzące dla `/images/user-512.png` , które zwykle oczekuje na znalezienie pliku obrazu na serwerze. Jeśli żądana ścieżka zasobu nie jest zgodna z żadną trasą po stronie serwera lub plikiem statycznym, jest mało prawdopodobne, że aplikacja po stronie klienta obsłuży jej &mdash; zwykle zwrócenie kodu stanu HTTP 404.

### <a name="routing-helpers-prerequisites"></a>Wymagania wstępne pomocników routingu

Zainstaluj pakiet npm routingu po stronie klienta. Użycie kątowe jako przykładu:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Konfiguracja pomocników routingu

Metoda rozszerzająca o nazwie `MapSpaFallbackRoute` jest używana w `Configure` metodzie:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Trasy są oceniane w kolejności, w jakiej są skonfigurowane. W związku z tym `default` trasa w poprzednim przykładzie kodu jest najpierw używana do dopasowania do wzorca.

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

Usługi JavaScript udostępniają wstępnie skonfigurowane szablony aplikacji. SpaServices jest używany w tych szablonach w połączeniu z różnymi strukturami i bibliotekami, takimi jak kątowy, reagowanie i Redux.

Te szablony można zainstalować za pośrednictwem interfejs wiersza polecenia platformy .NET Core, uruchamiając następujące polecenie:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Zostanie wyświetlona lista dostępnych szablonów SPA:

| Szablony                                 | Krótka nazwa | Język | Tagi        |
| ------------------------------------------| :--------: | :------: | :---------: |
| ASP.NET Core MVC ze Skośnością             | kątow    | Znajd     | Web/MVC/SPA |
| ASP.NET Core MVC z React.js            | biern      | Znajd     | Web/MVC/SPA |
| ASP.NET Core MVC z React.js i Redux  | reactredux | Znajd     | Web/MVC/SPA |

Aby utworzyć nowy projekt przy użyciu jednego z szablonów SPA, należy dołączyć **krótką nazwę** szablonu do polecenia [dotnet New](/dotnet/core/tools/dotnet-new) . Następujące polecenie tworzy aplikację kątową z ASP.NET Core MVC skonfigurowanym po stronie serwera:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Ustawianie trybu konfiguracji środowiska uruchomieniowego

Istnieją dwa podstawowe tryby konfiguracji środowiska uruchomieniowego:

* **Programowanie** :
  * Zawiera mapy źródłowe ułatwiające debugowanie.
  * Nie optymalizuje kodu po stronie klienta pod kątem wydajności.
* **Produkcja** :
  * Wyklucza mapy źródłowe.
  * Optymalizuje kod po stronie klienta za pomocą funkcji grupowania i minifikacja.

ASP.NET Core używa zmiennej środowiskowej o nazwie `ASPNETCORE_ENVIRONMENT` do przechowywania trybu konfiguracji. Aby uzyskać więcej informacji, zobacz [Ustawianie środowiska](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Uruchom z interfejs wiersza polecenia platformy .NET Core

Przywróć wymagane pakiety NuGet i npm, uruchamiając następujące polecenie w katalogu głównym projektu:

```dotnetcli
dotnet restore && npm i
```

Kompiluj i uruchom aplikację:

```dotnetcli
dotnet run
```

Aplikacja jest uruchamiana na hoście lokalnym zgodnie z [trybem konfiguracji środowiska uruchomieniowego](#set-the-runtime-configuration-mode). Przechodzenie do `http://localhost:5000` programu w przeglądarce powoduje wyświetlenie strony docelowej.

### <a name="run-with-visual-studio-2017"></a>Uruchamianie z programem Visual Studio 2017

Otwórz plik *. csproj* wygenerowany przez polecenie [dotnet New](/dotnet/core/tools/dotnet-new) . Wymagane pakiety NuGet i npm są przywracane automatycznie po otwarciu projektu. Ten proces przywracania może potrwać kilka minut, a aplikacja jest gotowa do uruchomienia po jego zakończeniu. Kliknij przycisk z zielonym uruchomieniem lub naciśnij klawisz `Ctrl + F5` , a w przeglądarce zostanie otwarta strona docelowa aplikacji. Aplikacja jest uruchamiana na hoście lokalnym zgodnie z [trybem konfiguracji środowiska uruchomieniowego](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Testowanie aplikacji

Szablony SpaServices są wstępnie skonfigurowane do uruchamiania testów po stronie klienta za pomocą [Karma](https://karma-runner.github.io/1.0/index.html) i [Jasmine](https://jasmine.github.io/). Jasmine to popularne środowisko testów jednostkowych dla języka JavaScript, a Karma to moduł uruchamiający testy dla tych testów. Karma jest skonfigurowany do współdziałania z [pakietem pośredniczącym tworzenia pakietów WebPack](#webpack-dev-middleware) w taki sposób, aby deweloperzy nie musieli zatrzymywać i uruchamiać testów przy każdym wprowadzeniu zmian. Niezależnie od tego, czy jest to kod działający w przypadku testowym lub przypadku testowego, test jest uruchamiany automatycznie.

Używając aplikacji kątowej jako przykładu, dwa przypadki testowe Jasmine są już podane dla `CounterComponent` pliku *Counter. Component. spec. TS* :

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Otwórz wiersz polecenia w katalogu *ClientApp* . Uruchom następujące polecenie:

```console
npm test
```

Skrypt uruchamia moduł Karma Test Runner, który odczytuje ustawienia zdefiniowane w pliku *karma.conf.js* . Oprócz innych ustawień *karma.conf.js* identyfikuje pliki testowe do wykonania za pośrednictwem `files` tablicy:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publikowanie aplikacji

Aby uzyskać więcej informacji na temat publikowania na platformie Azure, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/12474) w usłudze GitHub.

Połączenie wygenerowanych zasobów po stronie klienta i opublikowanych ASP.NET Core artefaktów w gotowy do wdrożenia pakiet może być kłopotliwe. Thankfully, SpaServices organizuje cały proces publikacji z niestandardowym elementem docelowym programu MSBuild o nazwie `RunWebpack` :

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Obiekt docelowy programu MSBuild ma następujące obowiązki:

1. Przywróć pakiety npm.
1. Utwórz kompilację klasy produkcyjnej innych firm, które są zasobami po stronie klienta.
1. Utwórz kompilację klasy produkcyjnej dla niestandardowych zasobów po stronie klienta.
1. Skopiuj zasoby wygenerowane przez pakiet WebPack do folderu publikowanie.

Obiekt docelowy MSBuild jest wywoływany podczas uruchamiania:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dokumenty kątowe](https://angular.io/docs)
