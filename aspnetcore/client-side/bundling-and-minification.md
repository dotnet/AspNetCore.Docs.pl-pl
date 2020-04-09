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
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="eb0c7-103">Pakiet i minify statycznych zasobów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb0c7-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="eb0c7-104">Przez [Scott Addie](https://twitter.com/Scott_Addie) i [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="eb0c7-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="eb0c7-105">W tym artykule wyjaśniono korzyści wynikające z zastosowania sprzedaży pakietowej i minimizacji, w tym sposób, w jaki te funkcje mogą być używane z aplikacjami internetowymi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="eb0c7-106">Czym jest łączenie i wydobywanie</span><span class="sxs-lookup"><span data-stu-id="eb0c7-106">What is bundling and minification</span></span>

<span data-ttu-id="eb0c7-107">Łączenie i wydobywanie to dwie różne optymalizacje wydajności, które można zastosować w aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="eb0c7-108">Używane razem, łączenie i wydobywanie zwiększają wydajność, zmniejszając liczbę żądań serwera i zmniejszając rozmiar żądanych zasobów statycznych.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="eb0c7-109">Łączenie i wydobywanie przede wszystkim skracają czas ładowania pierwszego żądania strony.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="eb0c7-110">Po zażądaniu strony sieci web przeglądarka buforuje zasoby statyczne (JavaScript, CSS i obrazy).</span><span class="sxs-lookup"><span data-stu-id="eb0c7-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="eb0c7-111">W związku z tym tworzenie pakietów i wydobywanie nie poprawia wydajności podczas żądania tej samej strony lub stron w tej samej witrynie żądającej tych samych zasobów.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="eb0c7-112">Jeśli nagłówek wygasa nie jest poprawnie ustawiony na zasobach, a tworzenie pakietów i minyfikacja nie jest używana, heurystyka świeżości przeglądarki oznacza, że zasoby są przestarzałe po kilku dniach.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="eb0c7-113">Ponadto przeglądarka wymaga żądania sprawdzania poprawności dla każdego zasobu.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="eb0c7-114">W takim przypadku łączenie i minimifikacja zapewniają poprawę wydajności nawet po pierwszym żądaniu strony.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="eb0c7-115">Łączenie</span><span class="sxs-lookup"><span data-stu-id="eb0c7-115">Bundling</span></span>

<span data-ttu-id="eb0c7-116">Tworzenie pakietów pozwala łączyć wiele plików w jeden plik.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="eb0c7-117">Tworzenie pakietów zmniejsza liczbę żądań serwera, które są niezbędne do renderowania zasobu sieci web, takiego jak strona internetowa.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="eb0c7-118">Możesz utworzyć dowolną liczbę pojedynczych pakietów specjalnie dla CSS, JavaScript itp. Mniejsza liczba plików oznacza mniej żądań HTTP z przeglądarki do serwera lub z usługi zapewniającej aplikację.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="eb0c7-119">Powoduje to lepszą wydajność ładowania pierwszej strony.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="eb0c7-120">Minification</span><span class="sxs-lookup"><span data-stu-id="eb0c7-120">Minification</span></span>

<span data-ttu-id="eb0c7-121">Minyfikacja usuwa niepotrzebne znaki z kodu bez zmiany funkcjonalności.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="eb0c7-122">Rezultatem jest znaczne zmniejszenie rozmiaru żądanych zasobów (takich jak CSS, obrazy i pliki JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb0c7-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="eb0c7-123">Typowe skutki uboczne minyfikacji obejmują skrócenie nazw zmiennych do jednego znaku i usunięcie komentarzy i niepotrzebnych odstępów.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="eb0c7-124">Należy wziąć pod uwagę następującą funkcję JavaScript:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="eb0c7-125">Minyfikacja zmniejsza funkcję do następujących:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="eb0c7-126">Oprócz usuwania komentarzy i niepotrzebnych odstępów, następujące nazwy parametrów i zmiennych zostały zmienione w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="eb0c7-127">Oryginał</span><span class="sxs-lookup"><span data-stu-id="eb0c7-127">Original</span></span> | <span data-ttu-id="eb0c7-128">Zmieniona</span><span class="sxs-lookup"><span data-stu-id="eb0c7-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="eb0c7-129">Wpływ sprzedaży pakietowej i mielenia</span><span class="sxs-lookup"><span data-stu-id="eb0c7-129">Impact of bundling and minification</span></span>

<span data-ttu-id="eb0c7-130">W poniższej tabeli przedstawiono różnice między indywidualnym ładowaniem aktywów a łączeniem i wydobywaniem:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="eb0c7-131">Akcja</span><span class="sxs-lookup"><span data-stu-id="eb0c7-131">Action</span></span> | <span data-ttu-id="eb0c7-132">Z B/M</span><span class="sxs-lookup"><span data-stu-id="eb0c7-132">With B/M</span></span> | <span data-ttu-id="eb0c7-133">Bez B/M</span><span class="sxs-lookup"><span data-stu-id="eb0c7-133">Without B/M</span></span> | <span data-ttu-id="eb0c7-134">Change</span><span class="sxs-lookup"><span data-stu-id="eb0c7-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="eb0c7-135">Żądania plików</span><span class="sxs-lookup"><span data-stu-id="eb0c7-135">File Requests</span></span>  | <span data-ttu-id="eb0c7-136">7</span><span class="sxs-lookup"><span data-stu-id="eb0c7-136">7</span></span>   | <span data-ttu-id="eb0c7-137">18</span><span class="sxs-lookup"><span data-stu-id="eb0c7-137">18</span></span>     | <span data-ttu-id="eb0c7-138">157%</span><span class="sxs-lookup"><span data-stu-id="eb0c7-138">157%</span></span>
<span data-ttu-id="eb0c7-139">Kb przeniesiony</span><span class="sxs-lookup"><span data-stu-id="eb0c7-139">KB Transferred</span></span> | <span data-ttu-id="eb0c7-140">156</span><span class="sxs-lookup"><span data-stu-id="eb0c7-140">156</span></span> | <span data-ttu-id="eb0c7-141">264.68</span><span class="sxs-lookup"><span data-stu-id="eb0c7-141">264.68</span></span> | <span data-ttu-id="eb0c7-142">70%</span><span class="sxs-lookup"><span data-stu-id="eb0c7-142">70%</span></span>
<span data-ttu-id="eb0c7-143">Czas ładowania (ms)</span><span class="sxs-lookup"><span data-stu-id="eb0c7-143">Load Time (ms)</span></span> | <span data-ttu-id="eb0c7-144">885</span><span class="sxs-lookup"><span data-stu-id="eb0c7-144">885</span></span> | <span data-ttu-id="eb0c7-145">2360</span><span class="sxs-lookup"><span data-stu-id="eb0c7-145">2360</span></span>   | <span data-ttu-id="eb0c7-146">167%</span><span class="sxs-lookup"><span data-stu-id="eb0c7-146">167%</span></span>

<span data-ttu-id="eb0c7-147">Przeglądarki są dość pełne w odniesieniu do nagłówków żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="eb0c7-148">Całkowita liczba wysłanych bajtów metryka odnotowała znaczne zmniejszenie podczas łączenia.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="eb0c7-149">Czas ładowania pokazuje znaczną poprawę, jednak w tym przykładzie uruchomiono lokalnie.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="eb0c7-150">Większy wzrost wydajności jest realizowany przy użyciu sprzedaży pakietowej i minimizacji z zasobami przeniesionymi za pośrednictwem sieci.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="eb0c7-151">Wybierz strategię sprzedaży pakietowej i minimizacji</span><span class="sxs-lookup"><span data-stu-id="eb0c7-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="eb0c7-152">Szablony projektów MVC i Razor Pages zapewniają gotowe rozwiązanie do łączenia i wydobywania składającego się z pliku konfiguracyjnego JSON.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="eb0c7-153">Narzędzia innych firm, takie jak [grunt](xref:client-side/using-grunt) runner zadań, wykonać te same zadania z nieco większą złożoność.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="eb0c7-154">Narzędzie innej firmy doskonale pasuje, gdy przepływ pracy dewelopera wymaga&mdash;przetwarzania poza tworzeniem pakietów i wydobywaniem, takimi jak linting i optymalizacja obrazu.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="eb0c7-155">Za pomocą tworzenia pakietów i minyfikacji w czasie projektowania, wstępnie zunifikowane pliki są tworzone przed wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="eb0c7-156">Łączenie i minifying przed wdrożeniem zapewnia zaletę mniejszego obciążenia serwera.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="eb0c7-157">Jednak ważne jest, aby rozpoznać, że tworzenie pakietów i minyfikacji w czasie projektowania zwiększa złożoność kompilacji i działa tylko z plikami statycznymi.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="eb0c7-158">Konfigurowanie tworzenia pakietów i minimizacji</span><span class="sxs-lookup"><span data-stu-id="eb0c7-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="eb0c7-159">W ASP.NET Core 2.0 lub wcześniejszych szablony projektów MVC i Razor Pages zawierają plik konfiguracyjny *bundleconfig.json,* który definiuje opcje dla każdego pakietu:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="eb0c7-160">W ASP.NET Core 2.1 lub nowszym dodaj nowy plik JSON o nazwie *bundleconfig.json*do katalogu głównego projektu MVC lub Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="eb0c7-161">Uwzględnij w tym pliku następujący JSON jako punkt wyjścia:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="eb0c7-162">Plik *bundleconfig.json* definiuje opcje dla każdego pakietu.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="eb0c7-163">W poprzednim przykładzie konfiguracja pojedynczego pakietu jest zdefiniowana dla niestandardowych plików JavaScript (*wwwroot/js/site.js*) i arkusza*stylów (wwwroot/css/site.css).*</span><span class="sxs-lookup"><span data-stu-id="eb0c7-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="eb0c7-164">Opcje konfiguracji obejmują:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-164">Configuration options include:</span></span>

* <span data-ttu-id="eb0c7-165">`outputFileName`: Nazwa pliku pakietu do wysiedli.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="eb0c7-166">Może zawierać ścieżkę względną z pliku *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="eb0c7-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="eb0c7-167">**Wymagane**</span><span class="sxs-lookup"><span data-stu-id="eb0c7-167">**required**</span></span>
* <span data-ttu-id="eb0c7-168">`inputFiles`: Tablica plików do łączenia.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="eb0c7-169">Są to ścieżki względne do pliku konfiguracyjnego.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="eb0c7-170">**opcjonalnie**, \*pusta wartość powoduje pusty plik wyjściowy.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="eb0c7-171">obsługiwane są wzory [globbingu.](https://www.tldp.org/LDP/abs/html/globbingref.html)</span><span class="sxs-lookup"><span data-stu-id="eb0c7-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="eb0c7-172">`minify`: Opcje minyfikacji dla typu wyjściowego.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="eb0c7-173">**opcjonalnie**, \*domyślnie - `minify: { enabled: true }` \*</span><span class="sxs-lookup"><span data-stu-id="eb0c7-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="eb0c7-174">Opcje konfiguracji są dostępne dla typu pliku wyjściowego.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="eb0c7-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="eb0c7-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="eb0c7-176">JavaScript Minifier</span><span class="sxs-lookup"><span data-stu-id="eb0c7-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="eb0c7-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="eb0c7-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="eb0c7-178">`includeInProject`: Flaga wskazująca, czy chcesz dodać wygenerowane pliki do pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="eb0c7-179">**opcjonalne**, *domyślnie - false*</span><span class="sxs-lookup"><span data-stu-id="eb0c7-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="eb0c7-180">`sourceMap`: Flaga wskazująca, czy chcesz wygenerować mapę źródłową dla dołączonego pliku.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="eb0c7-181">**opcjonalne**, *domyślnie - false*</span><span class="sxs-lookup"><span data-stu-id="eb0c7-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="eb0c7-182">`sourceMapRootPath`: Ścieżka główna do przechowywania wygenerowanego pliku mapy źródłowej.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="eb0c7-183">Wykonanie w czasie kompilacji sprzedaży pakietowej i minyfikacji</span><span class="sxs-lookup"><span data-stu-id="eb0c7-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="eb0c7-184">Pakiet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet umożliwia wykonywanie tworzenia pakietów i minyfikacji w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="eb0c7-185">Pakiet wstrzykuje [MSBuild cele,](/visualstudio/msbuild/msbuild-targets) które są uruchamiane w czasie kompilacji i czystego czasu.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="eb0c7-186">Plik *bundleconfig.json* jest analizowany przez proces kompilacji w celu wytworzenia plików wyjściowych na podstawie zdefiniowanej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="eb0c7-187">BuildBundlerMinifier należy do projektu opartego na społeczności w usłudze GitHub, dla którego firma Microsoft nie zapewnia pomocy technicznej.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="eb0c7-188">Kwestie powinny być złożone [tutaj](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="eb0c7-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb0c7-189">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb0c7-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb0c7-190">Dodaj pakiet *BuildBundlerMinifier* do projektu.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="eb0c7-191">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-191">Build the project.</span></span> <span data-ttu-id="eb0c7-192">W oknie Dane wyjściowe są wyświetlane następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-192">The following appears in the Output window:</span></span>

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

<span data-ttu-id="eb0c7-193">Oczyść projekt.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-193">Clean the project.</span></span> <span data-ttu-id="eb0c7-194">W oknie Dane wyjściowe są wyświetlane następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[<span data-ttu-id="eb0c7-195">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="eb0c7-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eb0c7-196">Dodaj pakiet *BuildBundlerMinifier* do projektu:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="eb0c7-197">Jeśli używasz ASP.NET Core 1.x, przywróć nowo dodany pakiet:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="eb0c7-198">Zbuduj projekt:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="eb0c7-199">Pojawi się następująca:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="eb0c7-200">Oczyść projekt:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="eb0c7-201">Zostaną wyświetlone następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="eb0c7-202">Doraźne wykonanie sprzedaży pakietowej i minyfikacji</span><span class="sxs-lookup"><span data-stu-id="eb0c7-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="eb0c7-203">Zadania łączenia i kopytowania można wykonywać ad hoc, bez budowania projektu.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="eb0c7-204">Dodaj pakiet [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet do projektu:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="eb0c7-205">BundlerMinifier.Core należy do projektu opartego na społeczności w usłudze GitHub, dla którego firma Microsoft nie zapewnia pomocy technicznej.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="eb0c7-206">Kwestie powinny być złożone [tutaj](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="eb0c7-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="eb0c7-207">Ten pakiet rozszerza wiersz polecenia .NET Core o narzędzie *dotnet-bundle.*</span><span class="sxs-lookup"><span data-stu-id="eb0c7-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="eb0c7-208">Następujące polecenie może być wykonane w oknie konsoli Menedżera pakietów (PMC) lub w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="eb0c7-209">Menedżer pakietów NuGet dodaje zależności do pliku `<PackageReference />` \*.csproj jako węzły.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="eb0c7-210">Polecenie `dotnet bundle` jest rejestrowane w wierszu polecenia `<DotNetCliToolReference />` .NET Core tylko wtedy, gdy używany jest węzeł.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="eb0c7-211">Odpowiednio zmodyfikuj plik \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="eb0c7-212">Dodawanie plików do przepływu pracy</span><span class="sxs-lookup"><span data-stu-id="eb0c7-212">Add files to workflow</span></span>

<span data-ttu-id="eb0c7-213">Rozważmy przykład, w którym dodaje się dodatkowy plik *custom.css* przypominający następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="eb0c7-214">Aby minify *custom.css* i spakować go z *site.css* do *pliku site.min.css,* dodaj ścieżkę względną do *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="eb0c7-215">Alternatywnie można użyć następującego wzoru globbingu:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="eb0c7-216">Ten wzorzec globbingu pasuje do wszystkich plików CSS i wyklucza rozdrobniony wzorzec pliku.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="eb0c7-217">Skompiluj aplikację.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-217">Build the application.</span></span> <span data-ttu-id="eb0c7-218">Otwórz *site.min.css* i zwróć uwagę, że zawartość *pliku custom.css* jest dołączana na końcu pliku.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="eb0c7-219">Łączenie i wydobywanie oparte na środowisku</span><span class="sxs-lookup"><span data-stu-id="eb0c7-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="eb0c7-220">Najlepszym rozwiązaniem jest, że dołączone i zunifikowane pliki aplikacji powinny być używane w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="eb0c7-221">Podczas tworzenia oryginalnych plików ułatwiają debugowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="eb0c7-222">Określ pliki, które mają być uwzględniane na stronach, korzystając z [pomocnika znaczników środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) w widokach.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="eb0c7-223">Pomocnik znaczników środowiska renderuje jego zawartość tylko podczas uruchamiania w określonych [środowiskach](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="eb0c7-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="eb0c7-224">Następujący `environment` tag renderuje nieprzetworzene pliki CSS podczas `Development` pracy w środowisku:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="eb0c7-225">Poniższy `environment` tag renderuje dołączone i zminifikowane pliki CSS podczas pracy w środowisku innym niż `Development`.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="eb0c7-226">Na przykład uruchomienie `Production` `Staging` lub wyzwala renderowanie tych arkuszy stylów:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="eb0c7-227">Spożywać bundleconfig.json z Gulp</span><span class="sxs-lookup"><span data-stu-id="eb0c7-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="eb0c7-228">Istnieją przypadki, w których przepływ pracy tworzenia pakietów i łączenia aplikacji wymaga dodatkowego przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="eb0c7-229">Przykłady obejmują optymalizację obrazu, krach pamięci podręcznej i przetwarzanie zasobów sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="eb0c7-230">Aby spełnić te wymagania, można przekonwertować przepływ pracy tworzenia pakietów i minyfikacji na użycie Gulp.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="eb0c7-231">Użyj rozszerzenia & Minifier Bundler</span><span class="sxs-lookup"><span data-stu-id="eb0c7-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="eb0c7-232">Rozszerzenie programu Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) obsługuje konwersję do Gulp.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="eb0c7-233">Rozszerzenie & Minifier należy do projektu opartego na społeczności w usłudze GitHub, dla którego firma Microsoft nie zapewnia pomocy technicznej.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="eb0c7-234">Kwestie powinny być złożone [tutaj](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="eb0c7-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="eb0c7-235">Kliknij prawym przyciskiem myszy plik *bundleconfig.json* w Eksploratorze rozwiązań i wybierz **opcję Bundler & Minifier** > **Convert To Gulp...**:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Konwertuj na element menu kontekstowego Łyk](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="eb0c7-237">Pliki *gulpfile.js* i *package.json* są dodawane do projektu.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="eb0c7-238">Obsługiwane pakiety [npm](https://www.npmjs.com/) wymienione w `devDependencies` sekcji pliku *package.json* są zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="eb0c7-239">Uruchom następujące polecenie w oknie PMC, aby zainstalować interfejs wiersza polecenia Gulp jako zależność globalną:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="eb0c7-240">Plik *gulpfile.js* odczytuje plik *bundleconfig.json* dla wejść, wyjść i ustawień.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="eb0c7-241">Konwertuj ręcznie</span><span class="sxs-lookup"><span data-stu-id="eb0c7-241">Convert manually</span></span>

<span data-ttu-id="eb0c7-242">Jeśli program Visual Studio i/lub rozszerzenie & Minifier pakietu są niedostępne, konwertuj ręcznie.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="eb0c7-243">Dodaj plik *package.json* z `devDependencies`następującymi plikami , do katalogu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="eb0c7-244">Moduł `gulp-uglify` nie obsługuje ecmascript (ES) 2015 / ES6 i nowsze.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="eb0c7-245">Zainstaluj [łyk-terser](https://www.npmjs.com/package/gulp-terser) `gulp-uglify` zamiast używać ES2015 / ES6 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="eb0c7-246">Zainstaluj zależności, uruchamiając następujące polecenie na tym samym poziomie co *package.json:*</span><span class="sxs-lookup"><span data-stu-id="eb0c7-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="eb0c7-247">Zainstaluj interfejs wiersza polecenia Gulp jako zależność globalną:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="eb0c7-248">Skopiuj plik *gulpfile.js* poniżej do katalogu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="eb0c7-249">Uruchamianie zadań łykania</span><span class="sxs-lookup"><span data-stu-id="eb0c7-249">Run Gulp tasks</span></span>

<span data-ttu-id="eb0c7-250">Aby wyzwolić zadanie łączenia Gulp przed kompilacją projektu w programie Visual Studio, dodaj następujący [obiekt docelowy MSBuild](/visualstudio/msbuild/msbuild-targets) do pliku \*.csproj:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="eb0c7-251">W tym przykładzie wszystkie `MyPreCompileTarget` zadania zdefiniowane w obrębie `Build` obiektu docelowego są uruchamiane przed wstępnie zdefiniowanym obiektem docelowym.</span><span class="sxs-lookup"><span data-stu-id="eb0c7-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="eb0c7-252">Dane wyjściowe podobne do następujących pojawia się w oknie dane wyjściowe programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="eb0c7-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="eb0c7-253">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="eb0c7-253">Additional resources</span></span>

* [<span data-ttu-id="eb0c7-254">Korzystanie z Grunt</span><span class="sxs-lookup"><span data-stu-id="eb0c7-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="eb0c7-255">Używanie wielu środowisk</span><span class="sxs-lookup"><span data-stu-id="eb0c7-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="eb0c7-256">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="eb0c7-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
