---
title: Łączenie i zminifikować zasobów statycznych w ASP.NET Core
author: scottaddie
description: Dowiedz się, jak zoptymalizować zasoby statyczne w ASP.NET Core aplikacji sieci Web przez zastosowanie technik tworzenia i minifikacja.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/02/2020
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: 7dd11ceb7a7c01ce1042f50595013b7fe7f1cd5c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054843"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="f6105-103">Łączenie i zminifikować zasobów statycznych w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6105-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="f6105-104">Przez [Scott Addie](https://twitter.com/Scott_Addie) i [David sosny](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="f6105-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="f6105-105">W tym artykule wyjaśniono zalety stosowania funkcji tworzenia i minifikacja, w tym ich używania z aplikacjami sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6105-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="f6105-106">Co to jest rozdziały i minifikacja</span><span class="sxs-lookup"><span data-stu-id="f6105-106">What is bundling and minification</span></span>

<span data-ttu-id="f6105-107">Tworzenie i minifikacja to dwie różne optymalizacje wydajności, które można zastosować w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6105-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="f6105-108">Używane razem, grupując i minifikacja poprawić wydajność poprzez zmniejszenie liczby żądań serwera i zmniejszenie rozmiaru żądanych zasobów statycznych.</span><span class="sxs-lookup"><span data-stu-id="f6105-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="f6105-109">Zgrupowanie i minifikacja przede wszystkim zwiększy czas ładowania żądania pierwszej strony.</span><span class="sxs-lookup"><span data-stu-id="f6105-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="f6105-110">Po zażądaniu strony sieci Web przeglądarka buforuje statyczne zasoby (JavaScript, CSS i obrazy).</span><span class="sxs-lookup"><span data-stu-id="f6105-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="f6105-111">W związku z tym, zgrupowanie i minifikacja nie poprawia wydajności podczas żądania tej samej strony lub stron w tej samej lokacji, w której zażądają tych samych zasobów.</span><span class="sxs-lookup"><span data-stu-id="f6105-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="f6105-112">Jeśli Nagłówek Expires nie jest poprawnie ustawiony na elementach zawartości i jeśli nie jest używane minifikacja i nie jest używany, heurystyka Aktualności przeglądarki Oznacz zasoby jako przestarzałe po kilku dniach.</span><span class="sxs-lookup"><span data-stu-id="f6105-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="f6105-113">Ponadto przeglądarka wymaga żądania weryfikacji dla każdego elementu zawartości.</span><span class="sxs-lookup"><span data-stu-id="f6105-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="f6105-114">W takim przypadku zgrupowanie i minifikacja zapewnia poprawę wydajności nawet po pierwszym żądaniu strony.</span><span class="sxs-lookup"><span data-stu-id="f6105-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="f6105-115">Tworzenia pakietów</span><span class="sxs-lookup"><span data-stu-id="f6105-115">Bundling</span></span>

<span data-ttu-id="f6105-116">Tworzenie pakietów pozwala łączyć wiele plików w jeden plik.</span><span class="sxs-lookup"><span data-stu-id="f6105-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="f6105-117">Zgrupowanie zmniejsza liczbę żądań serwera, które są niezbędne do renderowania zasobów sieci Web, takich jak strona sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6105-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="f6105-118">Można utworzyć dowolną liczbę pojedynczych pakietów przeznaczonych dla CSS, JavaScript itd. Mniejsza liczba plików oznacza mniejszą liczbę żądań HTTP z przeglądarki do serwera lub z usługi dostarczającej aplikację.</span><span class="sxs-lookup"><span data-stu-id="f6105-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="f6105-119">Powoduje to zwiększenie wydajności pierwszej strony.</span><span class="sxs-lookup"><span data-stu-id="f6105-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="f6105-120">Minifikacja</span><span class="sxs-lookup"><span data-stu-id="f6105-120">Minification</span></span>

<span data-ttu-id="f6105-121">Minifikacja usuwa zbędne znaki z kodu bez zmiany funkcjonalności.</span><span class="sxs-lookup"><span data-stu-id="f6105-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="f6105-122">Wynikiem jest znaczny spadek rozmiaru żądanych zasobów (takich jak CSS, obrazy i pliki JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f6105-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="f6105-123">Typowe efekty uboczne minifikacja obejmują skracanie nazw zmiennych do jednego znaku oraz usuwanie komentarzy i niepotrzebnych białych znaków.</span><span class="sxs-lookup"><span data-stu-id="f6105-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="f6105-124">Weź pod uwagę następującą funkcję języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="f6105-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="f6105-125">Minifikacja zmniejsza funkcję do następujących:</span><span class="sxs-lookup"><span data-stu-id="f6105-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="f6105-126">Oprócz usuwania komentarzy i niepotrzebnych białych znaków, nazwy następujących parametrów i zmiennych zostały zmienione w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="f6105-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="f6105-127">Oryginalne</span><span class="sxs-lookup"><span data-stu-id="f6105-127">Original</span></span> | <span data-ttu-id="f6105-128">Zmiany</span><span class="sxs-lookup"><span data-stu-id="f6105-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="f6105-129">Wpływ tworzenia i minifikacja</span><span class="sxs-lookup"><span data-stu-id="f6105-129">Impact of bundling and minification</span></span>

<span data-ttu-id="f6105-130">W poniższej tabeli przedstawiono różnice między pojedynczym ładowaniem zasobów i użyciem grupowania i minifikacja:</span><span class="sxs-lookup"><span data-stu-id="f6105-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="f6105-131">Akcja</span><span class="sxs-lookup"><span data-stu-id="f6105-131">Action</span></span> | <span data-ttu-id="f6105-132">Z B/M</span><span class="sxs-lookup"><span data-stu-id="f6105-132">With B/M</span></span> | <span data-ttu-id="f6105-133">Bez B/M</span><span class="sxs-lookup"><span data-stu-id="f6105-133">Without B/M</span></span> | <span data-ttu-id="f6105-134">Zmiana</span><span class="sxs-lookup"><span data-stu-id="f6105-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="f6105-135">Żądania plików</span><span class="sxs-lookup"><span data-stu-id="f6105-135">File Requests</span></span>  | <span data-ttu-id="f6105-136">7</span><span class="sxs-lookup"><span data-stu-id="f6105-136">7</span></span>   | <span data-ttu-id="f6105-137">18</span><span class="sxs-lookup"><span data-stu-id="f6105-137">18</span></span>     | <span data-ttu-id="f6105-138">157%</span><span class="sxs-lookup"><span data-stu-id="f6105-138">157%</span></span>
<span data-ttu-id="f6105-139">Przeniesiono KB</span><span class="sxs-lookup"><span data-stu-id="f6105-139">KB Transferred</span></span> | <span data-ttu-id="f6105-140">156</span><span class="sxs-lookup"><span data-stu-id="f6105-140">156</span></span> | <span data-ttu-id="f6105-141">264,68</span><span class="sxs-lookup"><span data-stu-id="f6105-141">264.68</span></span> | <span data-ttu-id="f6105-142">70%</span><span class="sxs-lookup"><span data-stu-id="f6105-142">70%</span></span>
<span data-ttu-id="f6105-143">Czas ładowania (MS)</span><span class="sxs-lookup"><span data-stu-id="f6105-143">Load Time (ms)</span></span> | <span data-ttu-id="f6105-144">885</span><span class="sxs-lookup"><span data-stu-id="f6105-144">885</span></span> | <span data-ttu-id="f6105-145">2360</span><span class="sxs-lookup"><span data-stu-id="f6105-145">2360</span></span>   | <span data-ttu-id="f6105-146">167%</span><span class="sxs-lookup"><span data-stu-id="f6105-146">167%</span></span>

<span data-ttu-id="f6105-147">Przeglądarki są dość szczegółowe w odniesieniu do nagłówków żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f6105-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="f6105-148">Metryka całkowita liczba wysłanych bajtów osiągnęła znaczącą redukcję podczas grupowania.</span><span class="sxs-lookup"><span data-stu-id="f6105-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="f6105-149">Czas ładowania przedstawia znaczącą poprawę, jednak ten przykład jest uruchamiany lokalnie.</span><span class="sxs-lookup"><span data-stu-id="f6105-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="f6105-150">W przypadku korzystania z funkcji grupowania i minifikacja z zasobami transferowanymi za pośrednictwem sieci są osiągane większe zyski wydajności.</span><span class="sxs-lookup"><span data-stu-id="f6105-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="f6105-151">Wybierz strategię tworzenia i minifikacja</span><span class="sxs-lookup"><span data-stu-id="f6105-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="f6105-152">RazorSzablony projektu MVC i Pages zapewniają rozwiązanie do tworzenia i minifikacja składające się z pliku konfiguracji JSON.</span><span class="sxs-lookup"><span data-stu-id="f6105-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="f6105-153">Narzędzia innych firm, takie jak [grunt](xref:client-side/using-grunt) Task Runner, spełniają te same zadania o nieco większej złożoności.</span><span class="sxs-lookup"><span data-stu-id="f6105-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="f6105-154">Narzędzie innej firmy jest doskonałym rozwiązaniem, gdy przepływ pracy deweloperskiej wymaga przetwarzania poza dzieleniem i minifikacja, &mdash; takim jak zaznaczanie błędów i Optymalizacja obrazu.</span><span class="sxs-lookup"><span data-stu-id="f6105-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="f6105-155">Korzystając z konstrukcji i minifikacja w czasie projektowania, pliki zminimalizowanego są tworzone przed wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6105-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="f6105-156">Przydzielenie i minifikacja przed wdrożeniem zapewnia zalety mniejszego obciążenia serwera.</span><span class="sxs-lookup"><span data-stu-id="f6105-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="f6105-157">Należy jednak pamiętać, że konstrukcja czasu projektowania i minifikacja zwiększa złożoność kompilacji i działa tylko z plikami statycznymi.</span><span class="sxs-lookup"><span data-stu-id="f6105-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="f6105-158">Konfigurowanie grupowania i minifikacja</span><span class="sxs-lookup"><span data-stu-id="f6105-158">Configure bundling and minification</span></span>

> [!NOTE]
> <span data-ttu-id="f6105-159">Aby to działało, należy dodać do projektu pakiet NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) .</span><span class="sxs-lookup"><span data-stu-id="f6105-159">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package needs to be added to your project for this to work.</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f6105-160">W ASP.NET Core 2,0 lub starszych szablon projektu MVC i Razor Pages udostępnia *bundleconfig.js* pliku konfiguracji, który definiuje opcje dla każdego pakietu:</span><span class="sxs-lookup"><span data-stu-id="f6105-160">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f6105-161">W ASP.NET Core 2,1 lub nowszej Dodaj nowy plik JSON o nazwie *bundleconfig.json*, do Razor elementu głównego projektu MVC lub Pages.</span><span class="sxs-lookup"><span data-stu-id="f6105-161">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="f6105-162">Dołącz następujący kod JSON do tego pliku jako punkt początkowy:</span><span class="sxs-lookup"><span data-stu-id="f6105-162">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="f6105-163">*bundleconfig.jsw* pliku definiuje opcje dla każdego pakietu.</span><span class="sxs-lookup"><span data-stu-id="f6105-163">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="f6105-164">W poprzednim przykładzie została zdefiniowana jedna konfiguracja pakietu dla plików niestandardowych JavaScript (*wwwroot/js/site.js*) i arkusza stylów (*wwwroot/CSS/site. css*).</span><span class="sxs-lookup"><span data-stu-id="f6105-164">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="f6105-165">Opcje konfiguracji obejmują:</span><span class="sxs-lookup"><span data-stu-id="f6105-165">Configuration options include:</span></span>

* <span data-ttu-id="f6105-166">`outputFileName`: Nazwa pliku pakietu do wyprowadzenia.</span><span class="sxs-lookup"><span data-stu-id="f6105-166">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="f6105-167">Może zawierać ścieżkę względną z *bundleconfig.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="f6105-167">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="f6105-168">**Wymagane**</span><span class="sxs-lookup"><span data-stu-id="f6105-168">**required**</span></span>
* <span data-ttu-id="f6105-169">`inputFiles`: Tablica plików do powiązania ze sobą.</span><span class="sxs-lookup"><span data-stu-id="f6105-169">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="f6105-170">Są to względne ścieżki do pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f6105-170">These are relative paths to the configuration file.</span></span> <span data-ttu-id="f6105-171">**opcjonalne**, \* pusta wartość powoduje pusty plik wyjściowy.</span><span class="sxs-lookup"><span data-stu-id="f6105-171">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="f6105-172">Obsługiwane są wzorce [obsługi symboli wieloznacznych](https://www.tldp.org/LDP/abs/html/globbingref.html) .</span><span class="sxs-lookup"><span data-stu-id="f6105-172">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="f6105-173">`minify`: Opcje minifikacja dla typu danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="f6105-173">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="f6105-174">**opcjonalne**, *domyślne — `minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="f6105-174">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="f6105-175">Opcje konfiguracji są dostępne dla każdego typu pliku wyjściowego.</span><span class="sxs-lookup"><span data-stu-id="f6105-175">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="f6105-176">Minifier CSS</span><span class="sxs-lookup"><span data-stu-id="f6105-176">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="f6105-177">Minifier JavaScript</span><span class="sxs-lookup"><span data-stu-id="f6105-177">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="f6105-178">Minifier HTML</span><span class="sxs-lookup"><span data-stu-id="f6105-178">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="f6105-179">`includeInProject`: Flaga oznaczająca, czy dodać wygenerowane pliki do pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="f6105-179">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="f6105-180">**opcjonalne**, *Domyślnie-false*</span><span class="sxs-lookup"><span data-stu-id="f6105-180">**optional**, *default - false*</span></span>
* <span data-ttu-id="f6105-181">`sourceMap`: Flaga oznaczająca, czy generować mapę źródłową dla powiązanego pliku.</span><span class="sxs-lookup"><span data-stu-id="f6105-181">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="f6105-182">**opcjonalne**, *Domyślnie-false*</span><span class="sxs-lookup"><span data-stu-id="f6105-182">**optional**, *default - false*</span></span>
* <span data-ttu-id="f6105-183">`sourceMapRootPath`: Ścieżka katalogu głównego do przechowywania wygenerowanego pliku mapy źródłowej.</span><span class="sxs-lookup"><span data-stu-id="f6105-183">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="f6105-184">Dodaj pliki do przepływu pracy</span><span class="sxs-lookup"><span data-stu-id="f6105-184">Add files to workflow</span></span>

<span data-ttu-id="f6105-185">Rozważmy przykład, w którym dodatkowy *niestandardowy plik CSS* został dodany podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="f6105-185">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="f6105-186">Aby zminifikować *niestandardowy. css* i powiązać go z plikiem *site. css* w pliku *site. min. css* , Dodaj ścieżkę względną do *bundleconfig.jsna*:</span><span class="sxs-lookup"><span data-stu-id="f6105-186">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="f6105-187">Alternatywnie można użyć następującego wzorca obsługi symboli wieloznacznych:</span><span class="sxs-lookup"><span data-stu-id="f6105-187">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="f6105-188">Ten wzorzec obsługi symboli wieloznacznych dopasowuje wszystkie pliki CSS i wyklucza wzorzec pliku zminimalizowanego.</span><span class="sxs-lookup"><span data-stu-id="f6105-188">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="f6105-189">Skompiluj aplikację.</span><span class="sxs-lookup"><span data-stu-id="f6105-189">Build the application.</span></span> <span data-ttu-id="f6105-190">Otwórz *witrynę site. min. css* i zwróć uwagę na zawartość *Custom. css* , która jest dołączana na końcu pliku.</span><span class="sxs-lookup"><span data-stu-id="f6105-190">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="f6105-191">Tworzenie i minifikacja oparte na środowisku</span><span class="sxs-lookup"><span data-stu-id="f6105-191">Environment-based bundling and minification</span></span>

<span data-ttu-id="f6105-192">Najlepszym rozwiązaniem jest użycie w środowisku produkcyjnym plików z pakietem i zminimalizowanego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6105-192">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="f6105-193">Podczas opracowywania oryginalne pliki ułatwiają debugowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6105-193">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="f6105-194">Określ pliki do uwzględnienia na stronach przy użyciu [pomocnika tagów środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) w widokach.</span><span class="sxs-lookup"><span data-stu-id="f6105-194">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="f6105-195">Pomocnik tagów środowiska renderuje jego zawartość tylko w przypadku uruchamiania w określonych [środowiskach](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f6105-195">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f6105-196">Następujący `environment` tag renderuje nieprzetworzone pliki CSS podczas działania w `Development` środowisku:</span><span class="sxs-lookup"><span data-stu-id="f6105-196">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="f6105-197">Poniższy `environment` tag renderuje powiązane i zminimalizowanego pliki CSS, gdy działa w środowisku innym niż `Development` .</span><span class="sxs-lookup"><span data-stu-id="f6105-197">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="f6105-198">Na przykład uruchomienie w programie `Production` lub `Staging` wyzwala renderowanie tych arkuszy stylów:</span><span class="sxs-lookup"><span data-stu-id="f6105-198">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="f6105-199">Korzystanie z bundleconfig.jsna podstawie Gulp</span><span class="sxs-lookup"><span data-stu-id="f6105-199">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="f6105-200">Istnieją przypadki, w których aplikacja i przepływy pracy minifikacja aplikacji wymagają dodatkowego przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="f6105-200">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="f6105-201">Przykładami są Optymalizacja obrazu, Busting pamięci podręcznej i przetwarzanie zasobów sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="f6105-201">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="f6105-202">Aby spełnić te wymagania, można skonwertować przepływ pracy tworzenia i minifikacja w celu użycia Gulp.</span><span class="sxs-lookup"><span data-stu-id="f6105-202">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="f6105-203">Ręcznie przekonwertuj przepływ pracy tworzenia i minifikacjaów, aby użyć Gulp</span><span class="sxs-lookup"><span data-stu-id="f6105-203">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="f6105-204">Dodaj *package.js* do pliku, w następujący `devDependencies` sposób, do katalogu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="f6105-204">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="f6105-205">`gulp-uglify`Moduł nie obsługuje języka ECMAScript (ES) 2015/ES6 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="f6105-205">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="f6105-206">Zainstaluj [Gulp-Terser](https://www.npmjs.com/package/gulp-terser) zamiast `gulp-uglify` używać ES2015/ES6 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="f6105-206">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="f6105-207">Zainstaluj zależności, uruchamiając następujące polecenie na tym samym poziomie co *package.jsna*:</span><span class="sxs-lookup"><span data-stu-id="f6105-207">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```bash
npm i
```

<span data-ttu-id="f6105-208">Zainstaluj interfejs wiersza polecenia Gulp jako zależność globalną:</span><span class="sxs-lookup"><span data-stu-id="f6105-208">Install the Gulp CLI as a global dependency:</span></span>

```bash
npm i -g gulp-cli
```

<span data-ttu-id="f6105-209">Skopiuj poniższy *gulpfile.js* plik do katalogu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="f6105-209">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="f6105-210">Uruchamianie zadań Gulp</span><span class="sxs-lookup"><span data-stu-id="f6105-210">Run Gulp tasks</span></span>

<span data-ttu-id="f6105-211">Aby wyzwolić zadanie Gulp minifikacja przed kompilacją projektu w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f6105-211">To trigger the Gulp minification task before the project builds in Visual Studio:</span></span>

1. <span data-ttu-id="f6105-212">Zainstaluj pakiet NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) .</span><span class="sxs-lookup"><span data-stu-id="f6105-212">Install the [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package.</span></span>
1. <span data-ttu-id="f6105-213">Dodaj następujący [element docelowy programu MSBuild](/visualstudio/msbuild/msbuild-targets) do pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="f6105-213">Add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the project file:</span></span>

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="f6105-214">W tym przykładzie wszystkie zadania zdefiniowane w `MyPreCompileTarget` miejscu docelowym są uruchamiane przed wstępnie zdefiniowanym `Build` elementem docelowym.</span><span class="sxs-lookup"><span data-stu-id="f6105-214">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="f6105-215">Dane wyjściowe podobne do następujących pojawiają się w oknie danych wyjściowych programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f6105-215">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f6105-216">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f6105-216">Additional resources</span></span>

* [<span data-ttu-id="f6105-217">Korzystanie z Grunt</span><span class="sxs-lookup"><span data-stu-id="f6105-217">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="f6105-218">Używanie wielu środowisk</span><span class="sxs-lookup"><span data-stu-id="f6105-218">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="f6105-219">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="f6105-219">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
