---
title: Łączenie i zminifikować zasobów statycznych w ASP.NET Core
author: scottaddie
description: Dowiedz się, jak zoptymalizować zasoby statyczne w ASP.NET Core aplikacji sieci Web przez zastosowanie technik tworzenia i minifikacja.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/14/2021
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
ms.openlocfilehash: d594bbf277907e22b0299b0451e480e9d533d506
ms.sourcegitcommit: 00368bb6a5420983beaced5b62dabc1f94abdeba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103557806"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="43fe3-103">Łączenie i zminifikować zasobów statycznych w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43fe3-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="43fe3-104">Przez [Scott Addie](https://twitter.com/Scott_Addie) i [David sosny](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="43fe3-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="43fe3-105">W tym artykule wyjaśniono zalety stosowania funkcji tworzenia i minifikacja, w tym ich używania z aplikacjami sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43fe3-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="43fe3-106">Co to jest rozdziały i minifikacja</span><span class="sxs-lookup"><span data-stu-id="43fe3-106">What is bundling and minification</span></span>

<span data-ttu-id="43fe3-107">Tworzenie i minifikacja to dwie różne optymalizacje wydajności, które można zastosować w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="43fe3-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="43fe3-108">Używane razem, grupując i minifikacja poprawić wydajność poprzez zmniejszenie liczby żądań serwera i zmniejszenie rozmiaru żądanych zasobów statycznych.</span><span class="sxs-lookup"><span data-stu-id="43fe3-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="43fe3-109">Zgrupowanie i minifikacja przede wszystkim zwiększy czas ładowania żądania pierwszej strony.</span><span class="sxs-lookup"><span data-stu-id="43fe3-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="43fe3-110">Po zażądaniu strony sieci Web przeglądarka buforuje statyczne zasoby (JavaScript, CSS i obrazy).</span><span class="sxs-lookup"><span data-stu-id="43fe3-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="43fe3-111">Dlatego zgrupowanie i minifikacja nie poprawiaj wydajności podczas żądania tej samej strony lub stron w tej samej lokacji, w której zażądają tych samych zasobów.</span><span class="sxs-lookup"><span data-stu-id="43fe3-111">So, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="43fe3-112">Jeśli Nagłówek Expires nie jest poprawnie ustawiony na elementach zawartości i jeśli nie jest używane minifikacja i nie jest używany, heurystyka Aktualności przeglądarki Oznacz zasoby jako przestarzałe po kilku dniach.</span><span class="sxs-lookup"><span data-stu-id="43fe3-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="43fe3-113">Ponadto przeglądarka wymaga żądania weryfikacji dla każdego elementu zawartości.</span><span class="sxs-lookup"><span data-stu-id="43fe3-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="43fe3-114">W takim przypadku zgrupowanie i minifikacja zapewnia poprawę wydajności nawet po pierwszym żądaniu strony.</span><span class="sxs-lookup"><span data-stu-id="43fe3-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="43fe3-115">Tworzenia pakietów</span><span class="sxs-lookup"><span data-stu-id="43fe3-115">Bundling</span></span>

<span data-ttu-id="43fe3-116">Tworzenie pakietów pozwala łączyć wiele plików w jeden plik.</span><span class="sxs-lookup"><span data-stu-id="43fe3-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="43fe3-117">Zgrupowanie zmniejsza liczbę żądań serwera, które są niezbędne do renderowania zasobów sieci Web, takich jak strona sieci Web.</span><span class="sxs-lookup"><span data-stu-id="43fe3-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="43fe3-118">Można utworzyć dowolną liczbę pojedynczych pakietów przeznaczonych dla CSS, JavaScript itd. Mniej plików oznacza mniejszą liczbę żądań HTTP z przeglądarki do serwera lub z usługi dostarczającej aplikację.</span><span class="sxs-lookup"><span data-stu-id="43fe3-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files mean fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="43fe3-119">Powoduje to zwiększenie wydajności pierwszej strony.</span><span class="sxs-lookup"><span data-stu-id="43fe3-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="43fe3-120">Minifikacja</span><span class="sxs-lookup"><span data-stu-id="43fe3-120">Minification</span></span>

<span data-ttu-id="43fe3-121">Minifikacja usuwa zbędne znaki z kodu bez zmiany funkcjonalności.</span><span class="sxs-lookup"><span data-stu-id="43fe3-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="43fe3-122">Wynikiem jest znaczny spadek rozmiaru żądanych zasobów (takich jak CSS, obrazy i pliki JavaScript).</span><span class="sxs-lookup"><span data-stu-id="43fe3-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="43fe3-123">Typowe efekty uboczne minifikacja obejmują skracanie nazw zmiennych do jednego znaku oraz usuwanie komentarzy i niepotrzebnych białych znaków.</span><span class="sxs-lookup"><span data-stu-id="43fe3-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="43fe3-124">Weź pod uwagę następującą funkcję języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="43fe3-124">Consider the following JavaScript function:</span></span>

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
    ///<signature>
    ///<summary> Adds an alt tab to the image
    // </summary>
    //<param name="imgElement" type="String">The image selector.</param>
    //<param name="ContextForImage" type="String">The image context.</param>
    ///</signature>
    var imageElement = $(imageTagAndImageID, imageContext);
    imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

<span data-ttu-id="43fe3-125">Minifikacja zmniejsza funkcję do następujących:</span><span class="sxs-lookup"><span data-stu-id="43fe3-125">Minification reduces the function to the following:</span></span>

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

<span data-ttu-id="43fe3-126">Oprócz usuwania komentarzy i niepotrzebnych białych znaków, nazwy następujących parametrów i zmiennych zostały zmienione w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="43fe3-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="43fe3-127">Oryginalne</span><span class="sxs-lookup"><span data-stu-id="43fe3-127">Original</span></span> | <span data-ttu-id="43fe3-128">Zmiany</span><span class="sxs-lookup"><span data-stu-id="43fe3-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="43fe3-129">Wpływ tworzenia i minifikacja</span><span class="sxs-lookup"><span data-stu-id="43fe3-129">Impact of bundling and minification</span></span>

<span data-ttu-id="43fe3-130">W poniższej tabeli przedstawiono różnice między pojedynczym ładowaniem zasobów i użyciem grupowania i minifikacja:</span><span class="sxs-lookup"><span data-stu-id="43fe3-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="43fe3-131">Akcja</span><span class="sxs-lookup"><span data-stu-id="43fe3-131">Action</span></span> | <span data-ttu-id="43fe3-132">Z B/M</span><span class="sxs-lookup"><span data-stu-id="43fe3-132">With B/M</span></span> | <span data-ttu-id="43fe3-133">Bez B/M</span><span class="sxs-lookup"><span data-stu-id="43fe3-133">Without B/M</span></span> | <span data-ttu-id="43fe3-134">Zmiana</span><span class="sxs-lookup"><span data-stu-id="43fe3-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="43fe3-135">Żądania plików</span><span class="sxs-lookup"><span data-stu-id="43fe3-135">File Requests</span></span>  | <span data-ttu-id="43fe3-136">7</span><span class="sxs-lookup"><span data-stu-id="43fe3-136">7</span></span>   | <span data-ttu-id="43fe3-137">18</span><span class="sxs-lookup"><span data-stu-id="43fe3-137">18</span></span>     | <span data-ttu-id="43fe3-138">157%</span><span class="sxs-lookup"><span data-stu-id="43fe3-138">157%</span></span>
<span data-ttu-id="43fe3-139">Przeniesiono KB</span><span class="sxs-lookup"><span data-stu-id="43fe3-139">KB Transferred</span></span> | <span data-ttu-id="43fe3-140">156</span><span class="sxs-lookup"><span data-stu-id="43fe3-140">156</span></span> | <span data-ttu-id="43fe3-141">264,68</span><span class="sxs-lookup"><span data-stu-id="43fe3-141">264.68</span></span> | <span data-ttu-id="43fe3-142">70%</span><span class="sxs-lookup"><span data-stu-id="43fe3-142">70%</span></span>
<span data-ttu-id="43fe3-143">Czas ładowania (MS)</span><span class="sxs-lookup"><span data-stu-id="43fe3-143">Load Time (ms)</span></span> | <span data-ttu-id="43fe3-144">885</span><span class="sxs-lookup"><span data-stu-id="43fe3-144">885</span></span> | <span data-ttu-id="43fe3-145">2360</span><span class="sxs-lookup"><span data-stu-id="43fe3-145">2360</span></span>   | <span data-ttu-id="43fe3-146">167%</span><span class="sxs-lookup"><span data-stu-id="43fe3-146">167%</span></span>

<span data-ttu-id="43fe3-147">Przeglądarki są dość pełne w odniesieniu do nagłówków żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="43fe3-147">Browsers are fairly verbose regarding HTTP request headers.</span></span> <span data-ttu-id="43fe3-148">Metryka całkowita liczba wysłanych bajtów osiągnęła znaczącą redukcję podczas grupowania.</span><span class="sxs-lookup"><span data-stu-id="43fe3-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="43fe3-149">Czas ładowania przedstawia znaczącą poprawę, jednak ten przykład jest uruchamiany lokalnie.</span><span class="sxs-lookup"><span data-stu-id="43fe3-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="43fe3-150">W przypadku korzystania z funkcji grupowania i minifikacja z zasobami transferowanymi za pośrednictwem sieci są osiągane większe zyski wydajności.</span><span class="sxs-lookup"><span data-stu-id="43fe3-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="43fe3-151">Wybierz strategię tworzenia i minifikacja</span><span class="sxs-lookup"><span data-stu-id="43fe3-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="43fe3-152">ASP.NET Core jest zgodna z programem weboptimize, minifikacja i rozwiązaniem Open Source.</span><span class="sxs-lookup"><span data-stu-id="43fe3-152">ASP.NET Core is compatible with WebOptimizer, an open-source bundling and minification solution.</span></span> <span data-ttu-id="43fe3-153">Aby uzyskać instrukcje dotyczące konfigurowania i przykładowych projektów, zobacz temat [Weboptimize](https://github.com/ligershark/WebOptimizer).</span><span class="sxs-lookup"><span data-stu-id="43fe3-153">For set up instructions and sample projects, see [WebOptimizer](https://github.com/ligershark/WebOptimizer).</span></span> <span data-ttu-id="43fe3-154">ASP.NET Core nie zapewnia natywnego tworzenia i minifikacja rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="43fe3-154">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span>

<span data-ttu-id="43fe3-155">Narzędzia innych firm, takie jak [Gulp](https://gulpjs.com) i [WebPack](https://webpack.js.org), zapewniają automatyzację przepływu pracy do tworzenia i minifikacja, a także Zaznaczanie błędów i optymalizację obrazu.</span><span class="sxs-lookup"><span data-stu-id="43fe3-155">Third-party tools, such as [Gulp](https://gulpjs.com) and [Webpack](https://webpack.js.org), provide workflow automation for bundling and minification, as well as linting and image optimization.</span></span> <span data-ttu-id="43fe3-156">Korzystając z konstrukcji i minifikacja w czasie projektowania, pliki zminimalizowanego są tworzone przed wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43fe3-156">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="43fe3-157">Przydzielenie i minifikacja przed wdrożeniem zapewnia zalety mniejszego obciążenia serwera.</span><span class="sxs-lookup"><span data-stu-id="43fe3-157">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="43fe3-158">Należy jednak pamiętać, że konstrukcja czasu projektowania i minifikacja zwiększa złożoność kompilacji i działa tylko z plikami statycznymi.</span><span class="sxs-lookup"><span data-stu-id="43fe3-158">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="43fe3-159">Tworzenie i minifikacja oparte na środowisku</span><span class="sxs-lookup"><span data-stu-id="43fe3-159">Environment-based bundling and minification</span></span>

<span data-ttu-id="43fe3-160">Najlepszym rozwiązaniem jest użycie w środowisku produkcyjnym plików z pakietem i zminimalizowanego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43fe3-160">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="43fe3-161">Podczas opracowywania oryginalne pliki ułatwiają debugowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43fe3-161">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="43fe3-162">Określ pliki do uwzględnienia na stronach przy użyciu [pomocnika tagów środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) w widokach.</span><span class="sxs-lookup"><span data-stu-id="43fe3-162">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="43fe3-163">Pomocnik tagów środowiska renderuje jego zawartość tylko w przypadku uruchamiania w określonych [środowiskach](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="43fe3-163">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="43fe3-164">Następujący `environment` tag renderuje nieprzetworzone pliki CSS podczas działania w `Development` środowisku:</span><span class="sxs-lookup"><span data-stu-id="43fe3-164">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment include="Development">
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    <link rel="stylesheet" href="~/css/site.css" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

<span data-ttu-id="43fe3-165">Poniższy `environment` tag renderuje powiązane i zminimalizowanego pliki CSS, gdy działa w środowisku innym niż `Development` .</span><span class="sxs-lookup"><span data-stu-id="43fe3-165">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="43fe3-166">Na przykład uruchomienie w programie `Production` lub `Staging` wyzwala renderowanie tych arkuszy stylów:</span><span class="sxs-lookup"><span data-stu-id="43fe3-166">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment exclude="Development">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="43fe3-167">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="43fe3-167">Additional resources</span></span>

* [<span data-ttu-id="43fe3-168">Używanie wielu środowisk</span><span class="sxs-lookup"><span data-stu-id="43fe3-168">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="43fe3-169">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="43fe3-169">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
