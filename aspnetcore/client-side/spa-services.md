---
title: Tworzenie aplikacji jednostronicowych w ASP.NET Core za pomocą usług JavaScript
author: scottaddie
description: Dowiedz się więcej o korzyściach płynących z korzystania z usług JavaScript w celu utworzenia aplikacji jednostronicowej (SPA) wspieranej przez ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663780"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Tworzenie aplikacji jednostronicowych w ASP.NET Core za pomocą usług JavaScript

Przez [Scott Addie](https://github.com/scottaddie) i [Fiyaz Hasan](https://fiyazhasan.me/)

Aplikacja jednostronicowa (SPA) jest popularnym typem aplikacji internetowej ze względu na jej nieodłączne bogate doświadczenie użytkownika. Integracja struktur lub bibliotek SPA po stronie klienta, takich jak [Angular](https://angular.io/) lub [React,](https://facebook.github.io/react/)z platformami po stronie serwera, takimi jak ASP.NET Core, może być trudna. Usługi JavaScript zostały opracowane w celu zmniejszenia tarcia w procesie integracji. Umożliwia bezproblemową obsługę między różnymi stosami technologii klienta i serwera.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Funkcje opisane w tym artykule są przestarzałe od ASP.NET Core 3.0. Prostszy mechanizm integracji struktur SPA jest dostępny w pakiecie [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet. Aby uzyskać więcej informacji, zobacz [[Anons] Obsoleting Microsoft.AspNetCore.SpaServices i Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Co to są usługi JavaScript

Usługi JavaScript to zbiór technologii po stronie klienta dla ASP.NET Core. Jego celem jest umieszczenie ASP.NET Core jako preferowanej platformy po stronie serwera programistów do tworzenia umów SPA.

Usługi JavaScript składają się z dwóch odrębnych pakietów NuGet:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (Usługi węzłowe)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (Usługi spa)

Te pakiety są przydatne w następujących scenariuszach:

* Uruchamianie języka JavaScript na serwerze
* Korzystanie z struktury lub biblioteki SPA
* Tworzenie zasobów po stronie klienta za pomocą pakietu WebPack

Wiele uwagi w tym artykule jest umieszczony na korzystanie z pakietu SpaServices.

## <a name="what-is-spaservices"></a>Co to jest SpaServices

SpaServices został stworzony, aby pozycjonować ASP.NET Core jako preferowaną platformę po stronie serwera programistów do tworzenia OSO. SpaServices nie jest wymagane do tworzenia oso z ASP.NET Core i nie blokuje deweloperów w określonej ramach klienta.

SpaServices zapewnia użyteczną infrastrukturę, taką jak:

* [Prerendering po stronie serwera](#server-side-prerendering)
* [Oprogramowanie pośredniczące w programach webpack Dev](#webpack-dev-middleware)
* [Wymiana modułu gorącego](#hot-module-replacement)
* [Pomocnicy routingu](#routing-helpers)

Łącznie te składniki infrastruktury zwiększają zarówno przepływ pracy deweloperów, jak i środowisko uruchomieniowe. Komponenty mogą być przyjmowane indywidualnie.

## <a name="prerequisites-for-using-spaservices"></a>Wymagania wstępne dotyczące korzystania z usług SpaServices

Aby pracować z SpaServices, zainstaluj następujące elementy:

* [Node.js](https://nodejs.org/) (wersja 6 lub nowsza) z npm

  * Aby sprawdzić, czy te składniki są zainstalowane i można je znaleźć, uruchom następujące czynności z wiersza polecenia:

    ```console
    node -v && npm -v
    ```

  * W przypadku wdrażania w witrynie sieci Web&mdash;platformy Azure nie jest wymagana żadna akcja Node.js jest zainstalowana i dostępna w środowiskach serwera.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * W systemie Windows przy użyciu programu Visual Studio 2017, SDK jest instalowany przez wybranie **.NET Core obciążenia deweloperskiego między platformami.**

* Pakiet [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet

## <a name="server-side-prerendering"></a>Prerendering po stronie serwera

Uniwersalna (znana również jako izomorficzna) aplikacja jest aplikacją JavaScript zdolną do uruchamiania zarówno na serwerze, jak i na kliencie. Angular, React i inne popularne struktury zapewniają uniwersalną platformę dla tego stylu tworzenia aplikacji. Chodzi o to, aby najpierw renderować składniki struktury na serwerze za pośrednictwem node.js, a następnie delegować dalsze wykonanie do klienta.

ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) dostarczone przez SpaServices upraszczają implementację prerendering po stronie serwera, wywołując funkcje JavaScript na serwerze.

### <a name="server-side-prerendering-prerequisites"></a>Wymagania wstępne po stronie serwera

Zainstaluj pakiet [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm:

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Konfiguracja prerenderingu po stronie serwera

Pomocników tagów są wykrywalne poprzez rejestrację obszaru nazw w pliku *_ViewImports.cshtml* projektu:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Te Pomocników Tag abstrakcji od zawiłości komunikowania się bezpośrednio z interfejsów API niskiego poziomu, wykorzystując składnię html-like wewnątrz widoku Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>asp-prerender-module Pomocnik znaczników

Pomocnik `asp-prerender-module` tagów, używany w poprzednim przykładzie kodu, wykonuje *ClientApp/dist/main-server.js* na serwerze za pośrednictwem node.js. Dla jasności, *main-server.js* plik jest artefaktem zadania transpilacji TypeScript-to-JavaScript w procesie kompilacji [webpack.](https://webpack.github.io/) Webpack definiuje alias punktu `main-server`wejścia ; oraz przechodzenie przez wykres zależności dla tego aliasu rozpoczyna się w pliku *ClientApp/boot-server.ts:*

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

W poniższym przykładzie Angular plik *ClientApp/boot-server.ts* wykorzystuje `createServerRenderer` funkcję i `RenderResult` typ pakietu `aspnet-prerendering` npm do konfigurowania renderowania serwera za pośrednictwem pliku Node.js. Znaczniki HTML przeznaczone do renderowania po stronie serwera są przekazywane do wywołania funkcji rozpoznawania, `Promise` które jest zawijane w silnie typiwany obiekt JavaScript. Istotność `Promise` obiektu polega na tym, że asynchronicznie dostarcza znaczników HTML do strony do iniekcji w elemencie zastępczym DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>asp-prerender-data Pomocnik znacznika

W połączeniu z `asp-prerender-module` Pomocnikiem znaczników pomocnik tagów `asp-prerender-data` może być używany do przekazywania informacji kontekstowych z widoku Razor do javascript po stronie serwera. Na przykład następujące znaczniki przekazuje dane `main-server` użytkownika do modułu:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Odebrany `UserName` argument jest serializowany przy użyciu wbudowanego serializatora JSON i jest przechowywany w `params.data` obiekcie. W poniższym przykładzie angular dane są używane do konstruowania spersonalizowane powitanie w elemencie: `h1`

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Nazwy właściwości przekazywane w Pomocników tagów są reprezentowane z notacją **PascalCase.** Kontrast, że do JavaScript, gdzie te same nazwy właściwości są reprezentowane z **camelCase**. Domyślna konfiguracja serializacji JSON jest odpowiedzialna za tę różnicę.

Aby rozwinąć w poprzednim przykładzie kodu, dane mogą być przekazywane z `globals` serwera do `resolve` widoku przez nawilżanie właściwości dostarczone do funkcji:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Tablica `postList` zdefiniowana `globals` wewnątrz obiektu jest dołączona `window` do obiektu globalnego przeglądarki. Ta zmienna podnoszenia do zakresu globalnego eliminuje powielanie wysiłku, zwłaszcza, że odnosi się do ładowania tych samych danych raz na serwerze i ponownie na kliencie.

![globalna zmienna postList dołączona do obiektu okna](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Oprogramowanie pośredniczące w programach webpack Dev

[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) wprowadza usprawniony przepływ pracy rozwoju, w którym Webpack buduje zasoby na żądanie. Oprogramowanie pośredniczące automatycznie kompiluje i obsługuje zasoby po stronie klienta, gdy strona jest ponownie ładowana w przeglądarce. Alternatywne podejście jest ręcznie wywołać Webpack za pośrednictwem skryptu kompilacji npm projektu, gdy zmienia się zależność innej firmy lub kod niestandardowy. Skrypt kompilacji npm w pliku *package.json* jest pokazany w poniższym przykładzie:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Wymagania wstępne oprogramowania pośredniczącego dev webpack

Zainstaluj pakiet [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Konfiguracja oprogramowania pośredniczącego webpack Dev

Oprogramowanie pośredniczące webpack Dev jest rejestrowane w potoku żądań HTTP `Configure` za pomocą następującego kodu w metodzie pliku *Startup.cs:*

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Metoda `UseWebpackDevMiddleware` rozszerzenia musi być wywoływana przed [zarejestrowaniem statycznego hostingu plików](xref:fundamentals/static-files) za pomocą metody `UseStaticFiles` rozszerzenia. Ze względów bezpieczeństwa zarejestruj oprogramowanie pośredniczące tylko wtedy, gdy aplikacja działa w trybie programowania.

Właściwość pliku *webpack.config.js* informuje oprogramowanie pośredniczące, aby obserwowało folder pod `dist` kątem zmian: `output.publicPath`

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Wymiana modułu gorącego

Pomyśl o Webpack Hot [Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) funkcja jako ewolucja [Webpack Dev Middleware](#webpack-dev-middleware). HmR wprowadza wszystkie te same korzyści, ale dodatkowo usprawnia przepływ pracy rozwoju, automatycznie aktualizując zawartość strony po kompilacji zmian. Nie należy mylić tego z odświeżeniem przeglądarki, co kolidowałoby z bieżącym stanem w pamięci i sesją debugowania spa. Istnieje aktywne łącze między usługą Webpack Dev Middleware a przeglądarką, co oznacza, że zmiany są wypychane do przeglądarki.

### <a name="hot-module-replacement-prerequisites"></a>Wymagania wstępne wymiany modułu gorącego

Zainstaluj pakiet [npm webpack-hot-middleware:](https://www.npmjs.com/package/webpack-hot-middleware)

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Konfiguracja wymiany gorącego modułu

Składnik HMR musi być zarejestrowany w potoku żądań HTTP MVC w metodzie: `Configure`

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Podobnie jak w przypadku oprogramowania `UseWebpackDevMiddleware` `UseStaticFiles` [pośredniczącego webpack Dev,](#webpack-dev-middleware)metoda rozszerzenia musi zostać wywołana przed metodą rozszerzenia. Ze względów bezpieczeństwa zarejestruj oprogramowanie pośredniczące tylko wtedy, gdy aplikacja działa w trybie programowania.

Plik *webpack.config.js* musi `plugins` definiować tablicę, nawet jeśli jest pusta:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Po załadowaniu aplikacji w przeglądarce karta Konsola narzędzi programistycznych zawiera potwierdzenie aktywacji HMR:

![Komunikat o wymianie modułu gorącego](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Pomocnicy routingu

W większości ASP.NET oso oparte na rdzeniu routing po stronie klienta jest często pożądane oprócz routingu po stronie serwera. Systemy routingu SPA i MVC mogą pracować niezależnie bez zakłóceń. Istnieje jednak jeden przypadek krawędzi stwarzające wyzwania: identyfikowanie 404 odpowiedzi HTTP.

Należy wziąć pod uwagę scenariusz, `/some/page` w którym jest używana trasa bez rozszerzenia. Załóżmy, że żądanie nie pasuje do trasy po stronie serwera, ale jego wzorzec pasuje do trasy po stronie klienta. Teraz należy wziąć `/images/user-512.png`pod uwagę przychodzące żądanie dla , który zazwyczaj oczekuje, aby znaleźć plik obrazu na serwerze. Jeśli żądana ścieżka zasobu nie pasuje do żadnej trasy po stronie serwera lub pliku statycznego,&mdash;jest mało prawdopodobne, że aplikacja po stronie klienta będzie obsługiwać go zazwyczaj zwracany kod stanu HTTP 404 jest pożądane.

### <a name="routing-helpers-prerequisites"></a>Wymagania wstępne pomocników routingu

Zainstaluj pakiet npm routingu po stronie klienta. Za pomocą Angular jako przykład:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Konfiguracja pomocników routingu

Metoda rozszerzenia `MapSpaFallbackRoute` o nazwie `Configure` jest używana w metodzie:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Trasy są oceniane w kolejności, w jakiej są skonfigurowane. W związku `default` z tym trasa w poprzednim przykładzie kodu jest używana najpierw do dopasowywania wzorców.

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

Usługi JavaScript zapewniają wstępnie skonfigurowane szablony aplikacji. SpaServices jest używany w tych szablonach w połączeniu z różnych struktur i bibliotek, takich jak Angular, React i Redux.

Te szablony można zainstalować za pomocą interfejsu wiersza polecenia .NET Core, uruchamiając następujące polecenie:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Zostanie wyświetlona lista dostępnych szablonów SPA:

| Szablony                                 | Krótka nazwa | Język | Tagi        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET rdzeń z kątowym             | Kątowe    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core z React.js            | Reagować      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core z React.js i Redux  | reactredux | [C#]     | Web/MVC/SPA |

Aby utworzyć nowy projekt przy użyciu jednego z szablonów SPA, należy dołączyć **krótką nazwę** szablonu w nowym poleceniu [dotnet.](/dotnet/core/tools/dotnet-new) Następujące polecenie tworzy aplikację kątową z ASP.NET Core MVC skonfigurowanym po stronie serwera:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Ustawianie trybu konfiguracji środowiska wykonawczego

Istnieją dwa podstawowe tryby konfiguracji środowiska uruchomieniowego:

* **Rozwój**:
  * Zawiera mapy źródłowe ułatwiające debugowanie.
  * Nie optymalizuje kodu po stronie klienta pod kątem wydajności.
* **Produkcja**:
  * Wyklucza mapy źródłowe.
  * Optymalizuje kod po stronie klienta poprzez łączenie i minyfikowanie.

ASP.NET Core używa zmiennej środowiskowej `ASPNETCORE_ENVIRONMENT` o nazwie do przechowywania trybu konfiguracji. Aby uzyskać więcej informacji, zobacz [Ustawianie środowiska](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Uruchamianie z rdzeniem polecenia .NET CORE CLI

Przywróć wymagane pakiety NuGet i npm, uruchamiając następujące polecenie w katalogu głównym projektu:

```dotnetcli
dotnet restore && npm i
```

Skompiluj i uruchom aplikację:

```dotnetcli
dotnet run
```

Aplikacja uruchamia się na localhost zgodnie z [trybem konfiguracji środowiska wykonawczego](#set-the-runtime-configuration-mode). Przejście do `http://localhost:5000` przeglądarki powoduje wyświetlenie strony docelowej.

### <a name="run-with-visual-studio-2017"></a>Uruchamianie za pomocą programu Visual Studio 2017

Otwórz plik *csproj* wygenerowany przez nowe polecenie [dotnet.](/dotnet/core/tools/dotnet-new) Wymagane pakiety NuGet i npm są przywracane automatycznie po otwarciu projektu. Ten proces przywracania może potrwać do kilku minut, a aplikacja jest gotowa do uruchomienia po jej zakończeniu. Kliknij zielony przycisk uruchom `Ctrl + F5`lub naciśnij przycisk , a przeglądarka otworzy stronę docelową aplikacji. Aplikacja działa na localhost zgodnie z [trybem konfiguracji środowiska wykonawczego](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Testowanie aplikacji

Szablony SpaServices są wstępnie skonfigurowane do uruchamiania testów po stronie klienta przy użyciu [karmy](https://karma-runner.github.io/1.0/index.html) i [jaśminu.](https://jasmine.github.io/) Jasmine jest popularnym frameworkiem do testowania jednostek javascript, podczas gdy Karma jest testowym biegaczem dla tych testów. Karma jest skonfigurowana do pracy z [oprogramowaniem pośredniczącym Webpack Dev](#webpack-dev-middleware) w taki sposób, że deweloper nie musi zatrzymywać i uruchamiać testu za każdym razem, gdy wprowadzane są zmiany. Niezależnie od tego, czy jest to kod uruchomiony dla przypadku testowego, czy sam przypadek testowy, test jest uruchamiany automatycznie.

Za pomocą aplikacji kątowej jako przykład, dwa przypadki testowe Jasmine są już przewidziane dla `CounterComponent` pliku *counter.component.spec.ts:*

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Otwórz wiersz polecenia w katalogu *ClientApp.* Uruchom następujące polecenie:

```console
npm test
```

Skrypt uruchamia biegacza testowego Karma, który odczytuje ustawienia zdefiniowane w pliku *karma.conf.js.* Wśród innych *ustawień, karma.conf.js* identyfikuje pliki testowe, `files` które mają być wykonane za pośrednictwem tablicy:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publikowanie aplikacji

Zobacz [ten problem z usługą GitHub,](https://github.com/dotnet/AspNetCore.Docs/issues/12474) aby uzyskać więcej informacji na temat publikowania na platformie Azure.

Łączenie wygenerowanych zasobów po stronie klienta i opublikowanych artefaktów ASP.NET core w gotowy do wdrożenia pakiet może być uciążliwe. Na szczęście SpaServices organizuje cały proces publikacji z niestandardowym `RunWebpack`celem MSBuild o nazwie:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Cel MSBuild ma następujące obowiązki:

1. Przywróć pakiety npm.
1. Tworzenie kompilacji klasy produkcyjnej zasobów innych firm po stronie klienta.
1. Tworzenie kompilacji klasy produkcyjnej niestandardowych zasobów po stronie klienta.
1. Skopiuj zasoby wygenerowane przez pakiet Web do folderu publikowania.

Obiekt docelowy MSBuild jest wywoływany podczas uruchamiania:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dokumenty kątowe](https://angular.io/docs)
