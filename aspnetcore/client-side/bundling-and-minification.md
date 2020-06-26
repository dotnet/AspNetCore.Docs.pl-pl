---
title: Łączenie i zminifikować zasobów statycznych w ASP.NET Core
author: scottaddie
description: Dowiedz się, jak zoptymalizować zasoby statyczne w ASP.NET Core aplikacji sieci Web przez zastosowanie technik tworzenia i minifikacja.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: de7c155189008e1f78bfb1eba062fcc86f9e4839
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401912"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Łączenie i zminifikować zasobów statycznych w ASP.NET Core

Przez [Scott Addie](https://twitter.com/Scott_Addie) i [David sosny](https://twitter.com/davidpine7)

W tym artykule wyjaśniono zalety stosowania funkcji tworzenia i minifikacja, w tym ich używania z aplikacjami sieci Web ASP.NET Core.

## <a name="what-is-bundling-and-minification"></a>Co to jest rozdziały i minifikacja

Tworzenie i minifikacja to dwie różne optymalizacje wydajności, które można zastosować w aplikacji sieci Web. Używane razem, grupując i minifikacja poprawić wydajność poprzez zmniejszenie liczby żądań serwera i zmniejszenie rozmiaru żądanych zasobów statycznych.

Zgrupowanie i minifikacja przede wszystkim zwiększy czas ładowania żądania pierwszej strony. Po zażądaniu strony sieci Web przeglądarka buforuje statyczne zasoby (JavaScript, CSS i obrazy). W związku z tym, zgrupowanie i minifikacja nie poprawia wydajności podczas żądania tej samej strony lub stron w tej samej lokacji, w której zażądają tych samych zasobów. Jeśli Nagłówek Expires nie jest poprawnie ustawiony na elementach zawartości i jeśli nie jest używane minifikacja i nie jest używany, heurystyka Aktualności przeglądarki Oznacz zasoby jako przestarzałe po kilku dniach. Ponadto przeglądarka wymaga żądania weryfikacji dla każdego elementu zawartości. W takim przypadku zgrupowanie i minifikacja zapewnia poprawę wydajności nawet po pierwszym żądaniu strony.

### <a name="bundling"></a>Tworzenia pakietów

Tworzenie pakietów pozwala łączyć wiele plików w jeden plik. Zgrupowanie zmniejsza liczbę żądań serwera, które są niezbędne do renderowania zasobów sieci Web, takich jak strona sieci Web. Można utworzyć dowolną liczbę pojedynczych pakietów przeznaczonych dla CSS, JavaScript itd. Mniejsza liczba plików oznacza mniejszą liczbę żądań HTTP z przeglądarki do serwera lub z usługi dostarczającej aplikację. Powoduje to zwiększenie wydajności pierwszej strony.

### <a name="minification"></a>Minifikacja

Minifikacja usuwa zbędne znaki z kodu bez zmiany funkcjonalności. Wynikiem jest znaczny spadek rozmiaru żądanych zasobów (takich jak CSS, obrazy i pliki JavaScript). Typowe efekty uboczne minifikacja obejmują skracanie nazw zmiennych do jednego znaku oraz usuwanie komentarzy i niepotrzebnych białych znaków.

Weź pod uwagę następującą funkcję języka JavaScript:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minifikacja zmniejsza funkcję do następujących:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Oprócz usuwania komentarzy i niepotrzebnych białych znaków, nazwy następujących parametrów i zmiennych zostały zmienione w następujący sposób:

Oryginał | Zmiany
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Wpływ tworzenia i minifikacja

W poniższej tabeli przedstawiono różnice między pojedynczym ładowaniem zasobów i użyciem grupowania i minifikacja:

Akcja | Z B/M | Bez B/M | Zmiana
--- | :---: | :---: | :---:
Żądania plików  | 7   | 18     | 157%
Przeniesiono KB | 156 | 264,68 | 70%
Czas ładowania (MS) | 885 | 2360   | 167%

Przeglądarki są dość szczegółowe w odniesieniu do nagłówków żądań HTTP. Metryka całkowita liczba wysłanych bajtów osiągnęła znaczącą redukcję podczas grupowania. Czas ładowania przedstawia znaczącą poprawę, jednak ten przykład jest uruchamiany lokalnie. W przypadku korzystania z funkcji grupowania i minifikacja z zasobami transferowanymi za pośrednictwem sieci są osiągane większe zyski wydajności.

## <a name="choose-a-bundling-and-minification-strategy"></a>Wybierz strategię tworzenia i minifikacja

RazorSzablony projektu MVC i Pages zapewniają rozwiązanie do tworzenia i minifikacja składające się z pliku konfiguracji JSON. Narzędzia innych firm, takie jak [grunt](xref:client-side/using-grunt) Task Runner, spełniają te same zadania o nieco większej złożoności. Narzędzie innej firmy jest doskonałym rozwiązaniem, gdy przepływ pracy deweloperskiej wymaga przetwarzania poza dzieleniem i minifikacja, &mdash; takim jak zaznaczanie błędów i Optymalizacja obrazu. Korzystając z konstrukcji i minifikacja w czasie projektowania, pliki zminimalizowanego są tworzone przed wdrożeniem aplikacji. Przydzielenie i minifikacja przed wdrożeniem zapewnia zalety mniejszego obciążenia serwera. Należy jednak pamiętać, że konstrukcja czasu projektowania i minifikacja zwiększa złożoność kompilacji i działa tylko z plikami statycznymi.

## <a name="configure-bundling-and-minification"></a>Konfigurowanie grupowania i minifikacja

::: moniker range="<= aspnetcore-2.0"

W ASP.NET Core 2,0 lub starszych szablon projektu MVC i Razor Pages udostępnia *bundleconfig.js* pliku konfiguracji, który definiuje opcje dla każdego pakietu:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

W ASP.NET Core 2,1 lub nowszej Dodaj nowy plik JSON o nazwie *bundleconfig.json*, do Razor elementu głównego projektu MVC lub Pages. Dołącz następujący kod JSON do tego pliku jako punkt początkowy:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*bundleconfig.jsw* pliku definiuje opcje dla każdego pakietu. W poprzednim przykładzie została zdefiniowana jedna konfiguracja pakietu dla plików niestandardowych JavaScript (*wwwroot/js/site.js*) i arkusza stylów (*wwwroot/CSS/site. css*).

Opcje konfiguracji obejmują:

* `outputFileName`: Nazwa pliku pakietu do wyprowadzenia. Może zawierać ścieżkę względną z *bundleconfig.js* pliku. **Wymagane**
* `inputFiles`: Tablica plików do powiązania ze sobą. Są to względne ścieżki do pliku konfiguracji. **opcjonalne**, * pusta wartość powoduje pusty plik wyjściowy. Obsługiwane są wzorce [obsługi symboli wieloznacznych](https://www.tldp.org/LDP/abs/html/globbingref.html) .
* `minify`: Opcje minifikacja dla typu danych wyjściowych. **opcjonalne**, *domyślne — `minify: { enabled: true }` *
  * Opcje konfiguracji są dostępne dla każdego typu pliku wyjściowego.
    * [Minifier CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minifier JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minifier HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Flaga oznaczająca, czy dodać wygenerowane pliki do pliku projektu. **opcjonalne**, *Domyślnie-false*
* `sourceMap`: Flaga oznaczająca, czy generować mapę źródłową dla powiązanego pliku. **opcjonalne**, *Domyślnie-false*
* `sourceMapRootPath`: Ścieżka katalogu głównego do przechowywania wygenerowanego pliku mapy źródłowej.

## <a name="add-files-to-workflow"></a>Dodaj pliki do przepływu pracy

Rozważmy przykład, w którym dodatkowy *niestandardowy plik CSS* został dodany podobny do poniższego:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Aby zminifikować *niestandardowy. css* i powiązać go z plikiem *site. css* w pliku *site. min. css* , Dodaj ścieżkę względną do *bundleconfig.jsna*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternatywnie można użyć następującego wzorca obsługi symboli wieloznacznych:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Ten wzorzec obsługi symboli wieloznacznych dopasowuje wszystkie pliki CSS i wyklucza wzorzec pliku zminimalizowanego.

Skompiluj aplikację. Otwórz *witrynę site. min. css* i zwróć uwagę na zawartość *Custom. css* , która jest dołączana na końcu pliku.

## <a name="environment-based-bundling-and-minification"></a>Tworzenie i minifikacja oparte na środowisku

Najlepszym rozwiązaniem jest użycie w środowisku produkcyjnym plików z pakietem i zminimalizowanego aplikacji. Podczas opracowywania oryginalne pliki ułatwiają debugowanie aplikacji.

Określ pliki do uwzględnienia na stronach przy użyciu [pomocnika tagów środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) w widokach. Pomocnik tagów środowiska renderuje jego zawartość tylko w przypadku uruchamiania w określonych [środowiskach](xref:fundamentals/environments).

Następujący `environment` tag renderuje nieprzetworzone pliki CSS podczas działania w `Development` środowisku:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Poniższy `environment` tag renderuje powiązane i zminimalizowanego pliki CSS, gdy działa w środowisku innym niż `Development` . Na przykład uruchomienie w programie `Production` lub `Staging` wyzwala renderowanie tych arkuszy stylów:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Korzystanie z bundleconfig.jsna podstawie Gulp

Istnieją przypadki, w których aplikacja i przepływy pracy minifikacja aplikacji wymagają dodatkowego przetwarzania. Przykładami są Optymalizacja obrazu, Busting pamięci podręcznej i przetwarzanie zasobów sieci CDN. Aby spełnić te wymagania, można skonwertować przepływ pracy tworzenia i minifikacja w celu użycia Gulp.

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Ręcznie przekonwertuj przepływ pracy tworzenia i minifikacjaów, aby użyć Gulp

Dodaj *package.js* do pliku, w następujący `devDependencies` sposób, do katalogu głównego projektu:

> [!WARNING]
> `gulp-uglify`Moduł nie obsługuje języka ECMAScript (ES) 2015/ES6 i nowszych. Zainstaluj [Gulp-Terser](https://www.npmjs.com/package/gulp-terser) zamiast `gulp-uglify` używać ES2015/ES6 lub nowszego.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Zainstaluj zależności, uruchamiając następujące polecenie na tym samym poziomie co *package.jsna*:

```console
npm i
```

Zainstaluj interfejs wiersza polecenia Gulp jako zależność globalną:

```console
npm i -g gulp-cli
```

Skopiuj poniższy *gulpfile.js* plik do katalogu głównego projektu:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Uruchamianie zadań Gulp

Aby wyzwolić zadanie Gulp minifikacja przed kompilacją projektu w programie Visual Studio, Dodaj następujący [obiekt docelowy programu MSBuild](/visualstudio/msbuild/msbuild-targets) do pliku *. csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

W tym przykładzie wszystkie zadania zdefiniowane w `MyPreCompileTarget` miejscu docelowym są uruchamiane przed wstępnie zdefiniowanym `Build` elementem docelowym. Dane wyjściowe podobne do następujących pojawiają się w oknie danych wyjściowych programu Visual Studio:

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
