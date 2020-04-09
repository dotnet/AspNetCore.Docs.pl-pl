---
title: Pakiet i minify statycznych zasobów w ASP.NET Core
author: scottaddie
description: Dowiedz się, jak zoptymalizować zasoby statyczne w aplikacji sieci web ASP.NET Core, stosując techniki łączenia i łączenia.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658271"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Pakiet i minify statycznych zasobów w ASP.NET Core

Przez [Scott Addie](https://twitter.com/Scott_Addie) i [David Pine](https://twitter.com/davidpine7)

W tym artykule wyjaśniono korzyści wynikające z zastosowania sprzedaży pakietowej i minimizacji, w tym sposób, w jaki te funkcje mogą być używane z aplikacjami internetowymi ASP.NET Core.

## <a name="what-is-bundling-and-minification"></a>Czym jest łączenie i wydobywanie

Łączenie i wydobywanie to dwie różne optymalizacje wydajności, które można zastosować w aplikacji internetowej. Używane razem, łączenie i wydobywanie zwiększają wydajność, zmniejszając liczbę żądań serwera i zmniejszając rozmiar żądanych zasobów statycznych.

Łączenie i wydobywanie przede wszystkim skracają czas ładowania pierwszego żądania strony. Po zażądaniu strony sieci web przeglądarka buforuje zasoby statyczne (JavaScript, CSS i obrazy). W związku z tym tworzenie pakietów i wydobywanie nie poprawia wydajności podczas żądania tej samej strony lub stron w tej samej witrynie żądającej tych samych zasobów. Jeśli nagłówek wygasa nie jest poprawnie ustawiony na zasobach, a tworzenie pakietów i minyfikacja nie jest używana, heurystyka świeżości przeglądarki oznacza, że zasoby są przestarzałe po kilku dniach. Ponadto przeglądarka wymaga żądania sprawdzania poprawności dla każdego zasobu. W takim przypadku łączenie i minimifikacja zapewniają poprawę wydajności nawet po pierwszym żądaniu strony.

### <a name="bundling"></a>Łączenie

Tworzenie pakietów pozwala łączyć wiele plików w jeden plik. Tworzenie pakietów zmniejsza liczbę żądań serwera, które są niezbędne do renderowania zasobu sieci web, takiego jak strona internetowa. Możesz utworzyć dowolną liczbę pojedynczych pakietów specjalnie dla CSS, JavaScript itp. Mniejsza liczba plików oznacza mniej żądań HTTP z przeglądarki do serwera lub z usługi zapewniającej aplikację. Powoduje to lepszą wydajność ładowania pierwszej strony.

### <a name="minification"></a>Minification

Minyfikacja usuwa niepotrzebne znaki z kodu bez zmiany funkcjonalności. Rezultatem jest znaczne zmniejszenie rozmiaru żądanych zasobów (takich jak CSS, obrazy i pliki JavaScript). Typowe skutki uboczne minyfikacji obejmują skrócenie nazw zmiennych do jednego znaku i usunięcie komentarzy i niepotrzebnych odstępów.

Należy wziąć pod uwagę następującą funkcję JavaScript:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minyfikacja zmniejsza funkcję do następujących:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Oprócz usuwania komentarzy i niepotrzebnych odstępów, następujące nazwy parametrów i zmiennych zostały zmienione w następujący sposób:

Oryginał | Zmieniona
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Wpływ sprzedaży pakietowej i mielenia

W poniższej tabeli przedstawiono różnice między indywidualnym ładowaniem aktywów a łączeniem i wydobywaniem:

Akcja | Z B/M | Bez B/M | Change
--- | :---: | :---: | :---:
Żądania plików  | 7   | 18     | 157%
Kb przeniesiony | 156 | 264.68 | 70%
Czas ładowania (ms) | 885 | 2360   | 167%

Przeglądarki są dość pełne w odniesieniu do nagłówków żądań HTTP. Całkowita liczba wysłanych bajtów metryka odnotowała znaczne zmniejszenie podczas łączenia. Czas ładowania pokazuje znaczną poprawę, jednak w tym przykładzie uruchomiono lokalnie. Większy wzrost wydajności jest realizowany przy użyciu sprzedaży pakietowej i minimizacji z zasobami przeniesionymi za pośrednictwem sieci.

## <a name="choose-a-bundling-and-minification-strategy"></a>Wybierz strategię sprzedaży pakietowej i minimizacji

Szablony projektów MVC i Razor Pages zapewniają gotowe rozwiązanie do łączenia i wydobywania składającego się z pliku konfiguracyjnego JSON. Narzędzia innych firm, takie jak [grunt](xref:client-side/using-grunt) runner zadań, wykonać te same zadania z nieco większą złożoność. Narzędzie innej firmy doskonale pasuje, gdy przepływ pracy dewelopera wymaga&mdash;przetwarzania poza tworzeniem pakietów i wydobywaniem, takimi jak linting i optymalizacja obrazu. Za pomocą tworzenia pakietów i minyfikacji w czasie projektowania, wstępnie zunifikowane pliki są tworzone przed wdrożeniem aplikacji. Łączenie i minifying przed wdrożeniem zapewnia zaletę mniejszego obciążenia serwera. Jednak ważne jest, aby rozpoznać, że tworzenie pakietów i minyfikacji w czasie projektowania zwiększa złożoność kompilacji i działa tylko z plikami statycznymi.

## <a name="configure-bundling-and-minification"></a>Konfigurowanie tworzenia pakietów i minimizacji

::: moniker range="<= aspnetcore-2.0"

W ASP.NET Core 2.0 lub wcześniejszych szablony projektów MVC i Razor Pages zawierają plik konfiguracyjny *bundleconfig.json,* który definiuje opcje dla każdego pakietu:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

W ASP.NET Core 2.1 lub nowszym dodaj nowy plik JSON o nazwie *bundleconfig.json*do katalogu głównego projektu MVC lub Razor Pages. Uwzględnij w tym pliku następujący JSON jako punkt wyjścia:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Plik *bundleconfig.json* definiuje opcje dla każdego pakietu. W poprzednim przykładzie konfiguracja pojedynczego pakietu jest zdefiniowana dla niestandardowych plików JavaScript (*wwwroot/js/site.js*) i arkusza*stylów (wwwroot/css/site.css).*

Opcje konfiguracji obejmują:

* `outputFileName`: Nazwa pliku pakietu do wysiedli. Może zawierać ścieżkę względną z pliku *bundleconfig.json.* **Wymagane**
* `inputFiles`: Tablica plików do łączenia. Są to ścieżki względne do pliku konfiguracyjnego. **opcjonalnie**, *pusta wartość powoduje pusty plik wyjściowy. obsługiwane są wzory [globbingu.](https://www.tldp.org/LDP/abs/html/globbingref.html)
* `minify`: Opcje minyfikacji dla typu wyjściowego. **opcjonalnie**, *domyślnie - `minify: { enabled: true }` *
  * Opcje konfiguracji są dostępne dla typu pliku wyjściowego.
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Flaga wskazująca, czy chcesz dodać wygenerowane pliki do pliku projektu. **opcjonalne**, *domyślnie - false*
* `sourceMap`: Flaga wskazująca, czy chcesz wygenerować mapę źródłową dla dołączonego pliku. **opcjonalne**, *domyślnie - false*
* `sourceMapRootPath`: Ścieżka główna do przechowywania wygenerowanego pliku mapy źródłowej.

## <a name="build-time-execution-of-bundling-and-minification"></a>Wykonanie w czasie kompilacji sprzedaży pakietowej i minyfikacji

Pakiet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet umożliwia wykonywanie tworzenia pakietów i minyfikacji w czasie kompilacji. Pakiet wstrzykuje [MSBuild cele,](/visualstudio/msbuild/msbuild-targets) które są uruchamiane w czasie kompilacji i czystego czasu. Plik *bundleconfig.json* jest analizowany przez proces kompilacji w celu wytworzenia plików wyjściowych na podstawie zdefiniowanej konfiguracji.

> [!NOTE]
> BuildBundlerMinifier należy do projektu opartego na społeczności w usłudze GitHub, dla którego firma Microsoft nie zapewnia pomocy technicznej. Kwestie powinny być złożone [tutaj](https://github.com/madskristensen/BundlerMinifier/issues).

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Dodaj pakiet *BuildBundlerMinifier* do projektu.

Skompiluj projekt. W oknie Dane wyjściowe są wyświetlane następujące informacje:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Oczyść projekt. W oknie Dane wyjściowe są wyświetlane następujące informacje:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Dodaj pakiet *BuildBundlerMinifier* do projektu:

```dotnetcli
dotnet add package BuildBundlerMinifier
```

Jeśli używasz ASP.NET Core 1.x, przywróć nowo dodany pakiet:

```dotnetcli
dotnet restore
```

Zbuduj projekt:

```dotnetcli
dotnet build
```

Pojawi się następująca:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Oczyść projekt:

```dotnetcli
dotnet clean
```

Zostaną wyświetlone następujące dane wyjściowe:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Doraźne wykonanie sprzedaży pakietowej i minyfikacji

Zadania łączenia i kopytowania można wykonywać ad hoc, bez budowania projektu. Dodaj pakiet [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet do projektu:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core należy do projektu opartego na społeczności w usłudze GitHub, dla którego firma Microsoft nie zapewnia pomocy technicznej. Kwestie powinny być złożone [tutaj](https://github.com/madskristensen/BundlerMinifier/issues).

Ten pakiet rozszerza wiersz polecenia .NET Core o narzędzie *dotnet-bundle.* Następujące polecenie może być wykonane w oknie konsoli Menedżera pakietów (PMC) lub w powłoce polecenia:

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> Menedżer pakietów NuGet dodaje zależności do pliku `<PackageReference />` *.csproj jako węzły. Polecenie `dotnet bundle` jest rejestrowane w wierszu polecenia `<DotNetCliToolReference />` .NET Core tylko wtedy, gdy używany jest węzeł. Odpowiednio zmodyfikuj plik *.csproj.

## <a name="add-files-to-workflow"></a>Dodawanie plików do przepływu pracy

Rozważmy przykład, w którym dodaje się dodatkowy plik *custom.css* przypominający następujące elementy:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Aby minify *custom.css* i spakować go z *site.css* do *pliku site.min.css,* dodaj ścieżkę względną do *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternatywnie można użyć następującego wzoru globbingu:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Ten wzorzec globbingu pasuje do wszystkich plików CSS i wyklucza rozdrobniony wzorzec pliku.

Skompiluj aplikację. Otwórz *site.min.css* i zwróć uwagę, że zawartość *pliku custom.css* jest dołączana na końcu pliku.

## <a name="environment-based-bundling-and-minification"></a>Łączenie i wydobywanie oparte na środowisku

Najlepszym rozwiązaniem jest, że dołączone i zunifikowane pliki aplikacji powinny być używane w środowisku produkcyjnym. Podczas tworzenia oryginalnych plików ułatwiają debugowanie aplikacji.

Określ pliki, które mają być uwzględniane na stronach, korzystając z [pomocnika znaczników środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) w widokach. Pomocnik znaczników środowiska renderuje jego zawartość tylko podczas uruchamiania w określonych [środowiskach](xref:fundamentals/environments).

Następujący `environment` tag renderuje nieprzetworzene pliki CSS podczas `Development` pracy w środowisku:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Poniższy `environment` tag renderuje dołączone i zminifikowane pliki CSS podczas pracy w środowisku innym niż `Development`. Na przykład uruchomienie `Production` `Staging` lub wyzwala renderowanie tych arkuszy stylów:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Spożywać bundleconfig.json z Gulp

Istnieją przypadki, w których przepływ pracy tworzenia pakietów i łączenia aplikacji wymaga dodatkowego przetwarzania. Przykłady obejmują optymalizację obrazu, krach pamięci podręcznej i przetwarzanie zasobów sieci CDN. Aby spełnić te wymagania, można przekonwertować przepływ pracy tworzenia pakietów i minyfikacji na użycie Gulp.

### <a name="use-the-bundler--minifier-extension"></a>Użyj rozszerzenia & Minifier Bundler

Rozszerzenie programu Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) obsługuje konwersję do Gulp.

> [!NOTE]
> Rozszerzenie & Minifier należy do projektu opartego na społeczności w usłudze GitHub, dla którego firma Microsoft nie zapewnia pomocy technicznej. Kwestie powinny być złożone [tutaj](https://github.com/madskristensen/BundlerMinifier/issues).

Kliknij prawym przyciskiem myszy plik *bundleconfig.json* w Eksploratorze rozwiązań i wybierz **opcję Bundler & Minifier** > **Convert To Gulp...**:

![Konwertuj na element menu kontekstowego Łyk](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

Pliki *gulpfile.js* i *package.json* są dodawane do projektu. Obsługiwane pakiety [npm](https://www.npmjs.com/) wymienione w `devDependencies` sekcji pliku *package.json* są zainstalowane.

Uruchom następujące polecenie w oknie PMC, aby zainstalować interfejs wiersza polecenia Gulp jako zależność globalną:

```console
npm i -g gulp-cli
```

Plik *gulpfile.js* odczytuje plik *bundleconfig.json* dla wejść, wyjść i ustawień.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Konwertuj ręcznie

Jeśli program Visual Studio i/lub rozszerzenie & Minifier pakietu są niedostępne, konwertuj ręcznie.

Dodaj plik *package.json* z `devDependencies`następującymi plikami , do katalogu głównego projektu:

> [!WARNING]
> Moduł `gulp-uglify` nie obsługuje ecmascript (ES) 2015 / ES6 i nowsze. Zainstaluj [łyk-terser](https://www.npmjs.com/package/gulp-terser) `gulp-uglify` zamiast używać ES2015 / ES6 lub nowszego.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Zainstaluj zależności, uruchamiając następujące polecenie na tym samym poziomie co *package.json:*

```console
npm i
```

Zainstaluj interfejs wiersza polecenia Gulp jako zależność globalną:

```console
npm i -g gulp-cli
```

Skopiuj plik *gulpfile.js* poniżej do katalogu głównego projektu:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Uruchamianie zadań łykania

Aby wyzwolić zadanie łączenia Gulp przed kompilacją projektu w programie Visual Studio, dodaj następujący [obiekt docelowy MSBuild](/visualstudio/msbuild/msbuild-targets) do pliku *.csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

W tym przykładzie wszystkie `MyPreCompileTarget` zadania zdefiniowane w obrębie `Build` obiektu docelowego są uruchamiane przed wstępnie zdefiniowanym obiektem docelowym. Dane wyjściowe podobne do następujących pojawia się w oknie dane wyjściowe programu Visual Studio:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Korzystanie z Grunt](xref:client-side/using-grunt)
* [Używanie wielu środowisk](xref:fundamentals/environments)
* [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro)
