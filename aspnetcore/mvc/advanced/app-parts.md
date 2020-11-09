---
title: 'Udostępnianie kontrolerów, widoków, Razor stron i nie tylko za pomocą części aplikacji w ASP.NET Core'
author: rick-anderson
description: 'Udostępnianie kontrolerów, widoku, Razor stron i innych elementów przy użyciu części aplikacji w ASP.NET Core'
ms.author: riande
ms.date: 11/11/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061187"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a><span data-ttu-id="052ce-103">Udostępnianie kontrolerów, widoków, Razor stron i nie tylko za pomocą części aplikacji</span><span class="sxs-lookup"><span data-stu-id="052ce-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="052ce-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="052ce-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="052ce-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="052ce-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="052ce-106">*Część aplikacji* to Abstrakcja zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="052ce-107">Części aplikacji umożliwiają ASP.NET Core odnajdywania kontrolerów, wyświetlania składników, pomocników tagów, Razor stron, źródeł kompilacji Razor i nie tylko.</span><span class="sxs-lookup"><span data-stu-id="052ce-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="052ce-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> jest częścią aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="052ce-109">`AssemblyPart` hermetyzuje odwołanie do zestawu i udostępnia typy i odwołania do kompilacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="052ce-110">[Dostawcy funkcji](#fp) pracują z częściami aplikacji, aby wypełnić funkcje aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="052ce-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="052ce-111">Głównym przypadkiem użycia części aplikacji jest skonfigurowanie aplikacji do odnajdywania (lub unikania ładowania) ASP.NET Core funkcji z zestawu.</span><span class="sxs-lookup"><span data-stu-id="052ce-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="052ce-112">Na przykład może być konieczne udostępnienie typowych funkcji między wieloma aplikacjami.</span><span class="sxs-lookup"><span data-stu-id="052ce-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="052ce-113">Korzystając z części aplikacji, można udostępnić zestaw (DLL) zawierający kontrolery, widoki, Razor strony, źródła kompilacji Razor, pomocników tagów i wiele więcej w przypadku wielu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="052ce-114">Udostępnianie zestawu jest preferowane do duplikowania kodu w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="052ce-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="052ce-115">ASP.NET Core aplikacje ładują funkcje z programu <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="052ce-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="052ce-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Klasa reprezentuje część aplikacji, która jest obsługiwana przez zestaw.</span><span class="sxs-lookup"><span data-stu-id="052ce-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="052ce-117">Załaduj funkcje ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="052ce-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="052ce-118">Użyj <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> klas i, <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> Aby odnajdywać i ładować funkcje ASP.NET Core (kontrolery, składniki widoku itp.).</span><span class="sxs-lookup"><span data-stu-id="052ce-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="052ce-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Śledzi dostępne części aplikacji i dostawców funkcji.</span><span class="sxs-lookup"><span data-stu-id="052ce-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="052ce-120">`ApplicationPartManager` jest skonfigurowany w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="052ce-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="052ce-121">Poniższy kod stanowi alternatywny sposób konfigurowania `ApplicationPartManager` użycia `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="052ce-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="052ce-122">Powyższe dwa przykłady kodu ładują `SharedController` z zestawu.</span><span class="sxs-lookup"><span data-stu-id="052ce-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="052ce-123">`SharedController`Nie znajduje się w projekcie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="052ce-124">Zobacz Pobieranie przykładowego [rozwiązania WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="052ce-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="052ce-125">Dołącz widoki</span><span class="sxs-lookup"><span data-stu-id="052ce-125">Include views</span></span>

<span data-ttu-id="052ce-126">Użyj [ Razor biblioteki klas](xref:razor-pages/ui-class) , aby dołączyć widoki w zestawie.</span><span class="sxs-lookup"><span data-stu-id="052ce-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="052ce-127">Zapobiegaj ładowaniu zasobów</span><span class="sxs-lookup"><span data-stu-id="052ce-127">Prevent loading resources</span></span>

<span data-ttu-id="052ce-128">Części aplikacji mogą służyć do *uniknięcia* ładowania zasobów w określonym zestawie lub lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="052ce-129">Dodaj lub Usuń elementy członkowskie  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekcji, aby ukryć lub udostępnić dostępne zasoby.</span><span class="sxs-lookup"><span data-stu-id="052ce-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="052ce-130">Kolejność wpisów w `ApplicationParts` kolekcji nie jest ważna.</span><span class="sxs-lookup"><span data-stu-id="052ce-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="052ce-131">Skonfiguruj `ApplicationPartManager` program przed użyciem go do konfigurowania usług w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="052ce-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="052ce-132">Na przykład skonfiguruj `ApplicationPartManager` przed wywołaniem `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="052ce-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="052ce-133">Wywołaj `Remove` `ApplicationParts` kolekcję, aby usunąć zasób.</span><span class="sxs-lookup"><span data-stu-id="052ce-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="052ce-134">`ApplicationPartManager`Zawiera części dla:</span><span class="sxs-lookup"><span data-stu-id="052ce-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="052ce-135">Zestaw aplikacji i zestawy zależne.</span><span class="sxs-lookup"><span data-stu-id="052ce-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="052ce-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="052ce-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="052ce-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="052ce-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="052ce-138">Dostawcy funkcji</span><span class="sxs-lookup"><span data-stu-id="052ce-138">Feature providers</span></span>

<span data-ttu-id="052ce-139">Dostawcy funkcji aplikacji badają części aplikacji i udostępniają funkcje dla tych części.</span><span class="sxs-lookup"><span data-stu-id="052ce-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="052ce-140">Istnieją Wbudowani dostawcy funkcji dla następujących funkcji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="052ce-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="052ce-141">`internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="052ce-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="052ce-142">Dostawcy funkcji dziedziczą z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , gdzie `T` jest typem funkcji.</span><span class="sxs-lookup"><span data-stu-id="052ce-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="052ce-143">Dostawców funkcji można zaimplementować dla dowolnego z wcześniej wymienionych typów funkcji.</span><span class="sxs-lookup"><span data-stu-id="052ce-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="052ce-144">Kolejność dostawców funkcji w programie `ApplicationPartManager.FeatureProviders` może mieć wpływ na zachowanie.</span><span class="sxs-lookup"><span data-stu-id="052ce-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="052ce-145">Później dodani dostawcy mogą reagować na akcje podejmowane przez wcześniej dodanych dostawców.</span><span class="sxs-lookup"><span data-stu-id="052ce-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="052ce-146">Wyświetlanie dostępnych funkcji</span><span class="sxs-lookup"><span data-stu-id="052ce-146">Display available features</span></span>

<span data-ttu-id="052ce-147">Funkcje dostępne dla aplikacji można wyliczyć, żądając od `ApplicationPartManager` [iniekcji zależności](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="052ce-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="052ce-148">[Przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) używa powyższego kodu, aby wyświetlić funkcje aplikacji:</span><span class="sxs-lookup"><span data-stu-id="052ce-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="052ce-149">Odnajdywanie w częściach aplikacji</span><span class="sxs-lookup"><span data-stu-id="052ce-149">Discovery in application parts</span></span>

<span data-ttu-id="052ce-150">Błędy HTTP 404 nie są rzadko stosowane podczas tworzenia przy użyciu części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="052ce-151">Te błędy są zwykle spowodowane brakiem zasadniczego wymagania w zakresie odnajdywania części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="052ce-152">Jeśli aplikacja zwróci błąd HTTP 404, sprawdź, czy zostały spełnione następujące wymagania:</span><span class="sxs-lookup"><span data-stu-id="052ce-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="052ce-153">`applicationName`Ustawienie musi być ustawione na zestaw główny używany do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="052ce-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="052ce-154">Zestaw główny używany do odnajdywania jest zwykle zestawem punktów wejścia.</span><span class="sxs-lookup"><span data-stu-id="052ce-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="052ce-155">Zestaw główny musi mieć odwołanie do części używanych do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="052ce-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="052ce-156">Odwołanie może być bezpośrednie lub przechodnie.</span><span class="sxs-lookup"><span data-stu-id="052ce-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="052ce-157">Zestaw główny musi odwoływać się do zestawu SDK sieci Web.</span><span class="sxs-lookup"><span data-stu-id="052ce-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="052ce-158">Struktura ma logikę, która składa się z atrybutów w zestawie głównym używanym do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="052ce-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="052ce-159">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="052ce-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="052ce-160">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="052ce-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="052ce-161">*Część aplikacji* to Abstrakcja zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="052ce-162">Części aplikacji umożliwiają ASP.NET Core odnajdywania kontrolerów, wyświetlania składników, pomocników tagów, Razor stron, źródeł kompilacji Razor i nie tylko.</span><span class="sxs-lookup"><span data-stu-id="052ce-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="052ce-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) jest częścią aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="052ce-164">`AssemblyPart` hermetyzuje odwołanie do zestawu i udostępnia typy i odwołania do kompilacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="052ce-165">*Dostawcy funkcji* pracują z częściami aplikacji, aby wypełnić funkcje aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="052ce-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="052ce-166">Głównym przypadkiem użycia części aplikacji jest skonfigurowanie aplikacji do odnajdywania (lub unikania ładowania) ASP.NET Core funkcji z zestawu.</span><span class="sxs-lookup"><span data-stu-id="052ce-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="052ce-167">Na przykład może być konieczne udostępnienie typowych funkcji między wieloma aplikacjami.</span><span class="sxs-lookup"><span data-stu-id="052ce-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="052ce-168">Korzystając z części aplikacji, można udostępnić zestaw (DLL) zawierający kontrolery, widoki, Razor strony, źródła kompilacji Razor, pomocników tagów i wiele więcej w przypadku wielu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="052ce-169">Udostępnianie zestawu jest preferowane do duplikowania kodu w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="052ce-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="052ce-170">ASP.NET Core aplikacje ładują funkcje z programu <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="052ce-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="052ce-171"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Klasa reprezentuje część aplikacji, która jest obsługiwana przez zestaw.</span><span class="sxs-lookup"><span data-stu-id="052ce-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="052ce-172">Załaduj funkcje ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="052ce-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="052ce-173">Użyj `ApplicationPart` klas i, `AssemblyPart` Aby odnajdywać i ładować funkcje ASP.NET Core (kontrolery, składniki widoku itp.).</span><span class="sxs-lookup"><span data-stu-id="052ce-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="052ce-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Śledzi dostępne części aplikacji i dostawców funkcji.</span><span class="sxs-lookup"><span data-stu-id="052ce-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="052ce-175">`ApplicationPartManager` jest skonfigurowany w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="052ce-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="052ce-176">Poniższy kod stanowi alternatywny sposób konfigurowania `ApplicationPartManager` użycia `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="052ce-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="052ce-177">Powyższe dwa przykłady kodu ładują `SharedController` z zestawu.</span><span class="sxs-lookup"><span data-stu-id="052ce-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="052ce-178">`SharedController`Nie znajduje się w projekcie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="052ce-179">Zobacz Pobieranie przykładowego [rozwiązania WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="052ce-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="052ce-180">Dołącz widoki</span><span class="sxs-lookup"><span data-stu-id="052ce-180">Include views</span></span>

<span data-ttu-id="052ce-181">Użyj [ Razor biblioteki klas](xref:razor-pages/ui-class) , aby dołączyć widoki w zestawie.</span><span class="sxs-lookup"><span data-stu-id="052ce-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="052ce-182">Zapobiegaj ładowaniu zasobów</span><span class="sxs-lookup"><span data-stu-id="052ce-182">Prevent loading resources</span></span>

<span data-ttu-id="052ce-183">Części aplikacji mogą służyć do *uniknięcia* ładowania zasobów w określonym zestawie lub lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="052ce-184">Dodaj lub Usuń elementy członkowskie  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekcji, aby ukryć lub udostępnić dostępne zasoby.</span><span class="sxs-lookup"><span data-stu-id="052ce-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="052ce-185">Kolejność wpisów w `ApplicationParts` kolekcji nie jest ważna.</span><span class="sxs-lookup"><span data-stu-id="052ce-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="052ce-186">Skonfiguruj `ApplicationPartManager` program przed użyciem go do konfigurowania usług w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="052ce-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="052ce-187">Na przykład skonfiguruj `ApplicationPartManager` przed wywołaniem `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="052ce-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="052ce-188">Wywołaj `Remove` `ApplicationParts` kolekcję, aby usunąć zasób.</span><span class="sxs-lookup"><span data-stu-id="052ce-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="052ce-189">Następujący kod używa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> do usunięcia `MyDependentLibrary` z aplikacji: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="052ce-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="052ce-190">`ApplicationPartManager`Zawiera części dla:</span><span class="sxs-lookup"><span data-stu-id="052ce-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="052ce-191">Zestaw aplikacji i zestawy zależne.</span><span class="sxs-lookup"><span data-stu-id="052ce-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="052ce-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="052ce-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="052ce-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="052ce-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="052ce-194">Dostawcy funkcji aplikacji</span><span class="sxs-lookup"><span data-stu-id="052ce-194">Application feature providers</span></span>

<span data-ttu-id="052ce-195">Dostawcy funkcji aplikacji badają części aplikacji i udostępniają funkcje dla tych części.</span><span class="sxs-lookup"><span data-stu-id="052ce-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="052ce-196">Istnieją Wbudowani dostawcy funkcji dla następujących funkcji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="052ce-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="052ce-197">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="052ce-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="052ce-198">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="052ce-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="052ce-199">Wyświetl składniki</span><span class="sxs-lookup"><span data-stu-id="052ce-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="052ce-200">Dostawcy funkcji dziedziczą z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , gdzie `T` jest typem funkcji.</span><span class="sxs-lookup"><span data-stu-id="052ce-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="052ce-201">Dostawców funkcji można zaimplementować dla dowolnego z wcześniej wymienionych typów funkcji.</span><span class="sxs-lookup"><span data-stu-id="052ce-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="052ce-202">Kolejność dostawców funkcji w programie `ApplicationPartManager.FeatureProviders` może mieć wpływ na zachowanie.</span><span class="sxs-lookup"><span data-stu-id="052ce-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="052ce-203">Później dodani dostawcy mogą reagować na akcje podejmowane przez wcześniej dodanych dostawców.</span><span class="sxs-lookup"><span data-stu-id="052ce-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="052ce-204">Wyświetlanie dostępnych funkcji</span><span class="sxs-lookup"><span data-stu-id="052ce-204">Display available features</span></span>

<span data-ttu-id="052ce-205">Funkcje dostępne dla aplikacji można wyliczyć, żądając od `ApplicationPartManager` [iniekcji zależności](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="052ce-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="052ce-206">[Przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) używa powyższego kodu, aby wyświetlić funkcje aplikacji:</span><span class="sxs-lookup"><span data-stu-id="052ce-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="052ce-207">Odnajdywanie w częściach aplikacji</span><span class="sxs-lookup"><span data-stu-id="052ce-207">Discovery in application parts</span></span>

<span data-ttu-id="052ce-208">Błędy HTTP 404 nie są rzadko stosowane podczas tworzenia przy użyciu części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="052ce-209">Te błędy są zwykle spowodowane brakiem zasadniczego wymagania w zakresie odnajdywania części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052ce-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="052ce-210">Jeśli aplikacja zwróci błąd HTTP 404, sprawdź, czy zostały spełnione następujące wymagania:</span><span class="sxs-lookup"><span data-stu-id="052ce-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="052ce-211">`applicationName`Ustawienie musi być ustawione na zestaw główny używany do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="052ce-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="052ce-212">Zestaw główny używany do odnajdywania jest zwykle zestawem punktów wejścia.</span><span class="sxs-lookup"><span data-stu-id="052ce-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="052ce-213">Zestaw główny musi mieć odwołanie do części używanych do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="052ce-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="052ce-214">Odwołanie może być bezpośrednie lub przechodnie.</span><span class="sxs-lookup"><span data-stu-id="052ce-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="052ce-215">Zestaw główny musi odwoływać się do zestawu SDK sieci Web.</span><span class="sxs-lookup"><span data-stu-id="052ce-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="052ce-216">Struktura ASP.NET Core ma niestandardową logikę kompilacji, która oznacza atrybuty w zestawie głównym, które są używane do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="052ce-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
