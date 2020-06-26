---
title: Udostępnianie kontrolerów, widoków, Razor stron i nie tylko za pomocą części aplikacji w ASP.NET Core
author: rick-anderson
description: Udostępnianie kontrolerów, widoku, Razor stron i innych elementów przy użyciu części aplikacji w ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/extensibility/app-parts
ms.openlocfilehash: cb1f8b045b8f2b143afc7895234733fbfb02cb07
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399754"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a><span data-ttu-id="54ddf-103">Udostępnianie kontrolerów, widoków, Razor stron i nie tylko za pomocą części aplikacji</span><span class="sxs-lookup"><span data-stu-id="54ddf-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54ddf-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="54ddf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="54ddf-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54ddf-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="54ddf-106">*Część aplikacji* to Abstrakcja zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="54ddf-107">Części aplikacji umożliwiają ASP.NET Core odnajdywania kontrolerów, wyświetlania składników, pomocników tagów, Razor stron, źródeł kompilacji Razor i nie tylko.</span><span class="sxs-lookup"><span data-stu-id="54ddf-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="54ddf-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>jest częścią aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="54ddf-109">`AssemblyPart`hermetyzuje odwołanie do zestawu i udostępnia typy i odwołania do kompilacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="54ddf-110">[Dostawcy funkcji](#fp) pracują z częściami aplikacji, aby wypełnić funkcje aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54ddf-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="54ddf-111">Głównym przypadkiem użycia części aplikacji jest skonfigurowanie aplikacji do odnajdywania (lub unikania ładowania) ASP.NET Core funkcji z zestawu.</span><span class="sxs-lookup"><span data-stu-id="54ddf-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="54ddf-112">Na przykład może być konieczne udostępnienie typowych funkcji między wieloma aplikacjami.</span><span class="sxs-lookup"><span data-stu-id="54ddf-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="54ddf-113">Korzystając z części aplikacji, można udostępnić zestaw (DLL) zawierający kontrolery, widoki, Razor strony, źródła kompilacji Razor, pomocników tagów i wiele więcej w przypadku wielu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="54ddf-114">Udostępnianie zestawu jest preferowane do duplikowania kodu w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="54ddf-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="54ddf-115">ASP.NET Core aplikacje ładują funkcje z programu <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="54ddf-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="54ddf-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Klasa reprezentuje część aplikacji, która jest obsługiwana przez zestaw.</span><span class="sxs-lookup"><span data-stu-id="54ddf-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="54ddf-117">Załaduj funkcje ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54ddf-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="54ddf-118">Użyj <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> klas i, <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> Aby odnajdywać i ładować funkcje ASP.NET Core (kontrolery, składniki widoku itp.).</span><span class="sxs-lookup"><span data-stu-id="54ddf-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="54ddf-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Śledzi dostępne części aplikacji i dostawców funkcji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="54ddf-120">`ApplicationPartManager`jest skonfigurowany w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="54ddf-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="54ddf-121">Poniższy kod stanowi alternatywny sposób konfigurowania `ApplicationPartManager` użycia `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="54ddf-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="54ddf-122">Powyższe dwa przykłady kodu ładują `SharedController` z zestawu.</span><span class="sxs-lookup"><span data-stu-id="54ddf-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="54ddf-123">`SharedController`Nie znajduje się w projekcie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="54ddf-124">Zobacz Pobieranie przykładowego [rozwiązania WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="54ddf-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="54ddf-125">Dołącz widoki</span><span class="sxs-lookup"><span data-stu-id="54ddf-125">Include views</span></span>

<span data-ttu-id="54ddf-126">Użyj [ Razor biblioteki klas](xref:razor-pages/ui-class) , aby dołączyć widoki w zestawie.</span><span class="sxs-lookup"><span data-stu-id="54ddf-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="54ddf-127">Zapobiegaj ładowaniu zasobów</span><span class="sxs-lookup"><span data-stu-id="54ddf-127">Prevent loading resources</span></span>

<span data-ttu-id="54ddf-128">Części aplikacji mogą służyć do *uniknięcia* ładowania zasobów w określonym zestawie lub lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="54ddf-129">Dodaj lub Usuń elementy członkowskie <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekcji, aby ukryć lub udostępnić dostępne zasoby.</span><span class="sxs-lookup"><span data-stu-id="54ddf-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="54ddf-130">Kolejność wpisów w `ApplicationParts` kolekcji nie jest ważna.</span><span class="sxs-lookup"><span data-stu-id="54ddf-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="54ddf-131">Skonfiguruj `ApplicationPartManager` program przed użyciem go do konfigurowania usług w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="54ddf-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="54ddf-132">Na przykład skonfiguruj `ApplicationPartManager` przed wywołaniem `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="54ddf-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="54ddf-133">Wywołaj `Remove` `ApplicationParts` kolekcję, aby usunąć zasób.</span><span class="sxs-lookup"><span data-stu-id="54ddf-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="54ddf-134">`ApplicationPartManager`Zawiera części dla:</span><span class="sxs-lookup"><span data-stu-id="54ddf-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="54ddf-135">Zestaw aplikacji i zestawy zależne.</span><span class="sxs-lookup"><span data-stu-id="54ddf-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="54ddf-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="54ddf-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="54ddf-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="54ddf-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="54ddf-138">Dostawcy funkcji</span><span class="sxs-lookup"><span data-stu-id="54ddf-138">Feature providers</span></span>

<span data-ttu-id="54ddf-139">Dostawcy funkcji aplikacji badają części aplikacji i udostępniają funkcje dla tych części.</span><span class="sxs-lookup"><span data-stu-id="54ddf-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="54ddf-140">Istnieją Wbudowani dostawcy funkcji dla następujących funkcji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="54ddf-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="54ddf-141">`internal class`[RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="54ddf-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="54ddf-142">Dostawcy funkcji dziedziczą z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , gdzie `T` jest typem funkcji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="54ddf-143">Dostawców funkcji można zaimplementować dla dowolnego z wcześniej wymienionych typów funkcji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="54ddf-144">Kolejność dostawców funkcji w programie `ApplicationPartManager.FeatureProviders` może mieć wpływ na zachowanie.</span><span class="sxs-lookup"><span data-stu-id="54ddf-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="54ddf-145">Później dodani dostawcy mogą reagować na akcje podejmowane przez wcześniej dodanych dostawców.</span><span class="sxs-lookup"><span data-stu-id="54ddf-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="54ddf-146">Wyświetlanie dostępnych funkcji</span><span class="sxs-lookup"><span data-stu-id="54ddf-146">Display available features</span></span>

<span data-ttu-id="54ddf-147">Funkcje dostępne dla aplikacji można wyliczyć, żądając od `ApplicationPartManager` [iniekcji zależności](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="54ddf-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="54ddf-148">[Przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) używa powyższego kodu, aby wyświetlić funkcje aplikacji:</span><span class="sxs-lookup"><span data-stu-id="54ddf-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

## <a name="discovery-in-application-parts"></a><span data-ttu-id="54ddf-149">Odnajdywanie w częściach aplikacji</span><span class="sxs-lookup"><span data-stu-id="54ddf-149">Discovery in application parts</span></span>

<span data-ttu-id="54ddf-150">Błędy HTTP 404 nie są rzadko stosowane podczas tworzenia przy użyciu części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="54ddf-151">Te błędy są zwykle spowodowane brakiem zasadniczego wymagania w zakresie odnajdywania części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="54ddf-152">Jeśli aplikacja zwróci błąd HTTP 404, sprawdź, czy zostały spełnione następujące wymagania:</span><span class="sxs-lookup"><span data-stu-id="54ddf-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="54ddf-153">`applicationName`Ustawienie musi być ustawione na zestaw główny używany do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="54ddf-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="54ddf-154">Zestaw główny używany do odnajdywania jest zwykle zestawem punktów wejścia.</span><span class="sxs-lookup"><span data-stu-id="54ddf-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="54ddf-155">Zestaw główny musi mieć odwołanie do części używanych do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="54ddf-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="54ddf-156">Odwołanie może być bezpośrednie lub przechodnie.</span><span class="sxs-lookup"><span data-stu-id="54ddf-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="54ddf-157">Zestaw główny musi odwoływać się do zestawu SDK sieci Web.</span><span class="sxs-lookup"><span data-stu-id="54ddf-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="54ddf-158">Struktura ma logikę, która składa się z atrybutów w zestawie głównym używanym do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="54ddf-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="54ddf-159">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="54ddf-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="54ddf-160">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54ddf-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="54ddf-161">*Część aplikacji* to Abstrakcja zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="54ddf-162">Części aplikacji umożliwiają ASP.NET Core odnajdywania kontrolerów, wyświetlania składników, pomocników tagów, Razor stron, źródeł kompilacji Razor i nie tylko.</span><span class="sxs-lookup"><span data-stu-id="54ddf-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="54ddf-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) jest częścią aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="54ddf-164">`AssemblyPart`hermetyzuje odwołanie do zestawu i udostępnia typy i odwołania do kompilacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="54ddf-165">*Dostawcy funkcji* pracują z częściami aplikacji, aby wypełnić funkcje aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54ddf-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="54ddf-166">Głównym przypadkiem użycia części aplikacji jest skonfigurowanie aplikacji do odnajdywania (lub unikania ładowania) ASP.NET Core funkcji z zestawu.</span><span class="sxs-lookup"><span data-stu-id="54ddf-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="54ddf-167">Na przykład może być konieczne udostępnienie typowych funkcji między wieloma aplikacjami.</span><span class="sxs-lookup"><span data-stu-id="54ddf-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="54ddf-168">Korzystając z części aplikacji, można udostępnić zestaw (DLL) zawierający kontrolery, widoki, Razor strony, źródła kompilacji Razor, pomocników tagów i wiele więcej w przypadku wielu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="54ddf-169">Udostępnianie zestawu jest preferowane do duplikowania kodu w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="54ddf-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="54ddf-170">ASP.NET Core aplikacje ładują funkcje z programu <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="54ddf-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="54ddf-171"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Klasa reprezentuje część aplikacji, która jest obsługiwana przez zestaw.</span><span class="sxs-lookup"><span data-stu-id="54ddf-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="54ddf-172">Załaduj funkcje ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54ddf-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="54ddf-173">Użyj `ApplicationPart` klas i, `AssemblyPart` Aby odnajdywać i ładować funkcje ASP.NET Core (kontrolery, składniki widoku itp.).</span><span class="sxs-lookup"><span data-stu-id="54ddf-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="54ddf-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Śledzi dostępne części aplikacji i dostawców funkcji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="54ddf-175">`ApplicationPartManager`jest skonfigurowany w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="54ddf-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="54ddf-176">Poniższy kod stanowi alternatywny sposób konfigurowania `ApplicationPartManager` użycia `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="54ddf-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="54ddf-177">Powyższe dwa przykłady kodu ładują `SharedController` z zestawu.</span><span class="sxs-lookup"><span data-stu-id="54ddf-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="54ddf-178">`SharedController`Nie znajduje się w projekcie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="54ddf-179">Zobacz Pobieranie przykładowego [rozwiązania WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="54ddf-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="54ddf-180">Dołącz widoki</span><span class="sxs-lookup"><span data-stu-id="54ddf-180">Include views</span></span>

<span data-ttu-id="54ddf-181">Użyj [ Razor biblioteki klas](xref:razor-pages/ui-class) , aby dołączyć widoki w zestawie.</span><span class="sxs-lookup"><span data-stu-id="54ddf-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="54ddf-182">Zapobiegaj ładowaniu zasobów</span><span class="sxs-lookup"><span data-stu-id="54ddf-182">Prevent loading resources</span></span>

<span data-ttu-id="54ddf-183">Części aplikacji mogą służyć do *uniknięcia* ładowania zasobów w określonym zestawie lub lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="54ddf-184">Dodaj lub Usuń elementy członkowskie <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekcji, aby ukryć lub udostępnić dostępne zasoby.</span><span class="sxs-lookup"><span data-stu-id="54ddf-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="54ddf-185">Kolejność wpisów w `ApplicationParts` kolekcji nie jest ważna.</span><span class="sxs-lookup"><span data-stu-id="54ddf-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="54ddf-186">Skonfiguruj `ApplicationPartManager` program przed użyciem go do konfigurowania usług w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="54ddf-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="54ddf-187">Na przykład skonfiguruj `ApplicationPartManager` przed wywołaniem `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="54ddf-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="54ddf-188">Wywołaj `Remove` `ApplicationParts` kolekcję, aby usunąć zasób.</span><span class="sxs-lookup"><span data-stu-id="54ddf-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="54ddf-189">Następujący kod używa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> do usunięcia `MyDependentLibrary` z aplikacji:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="54ddf-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="54ddf-190">`ApplicationPartManager`Zawiera części dla:</span><span class="sxs-lookup"><span data-stu-id="54ddf-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="54ddf-191">Zestaw aplikacji i zestawy zależne.</span><span class="sxs-lookup"><span data-stu-id="54ddf-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="54ddf-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="54ddf-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="54ddf-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="54ddf-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="54ddf-194">Dostawcy funkcji aplikacji</span><span class="sxs-lookup"><span data-stu-id="54ddf-194">Application feature providers</span></span>

<span data-ttu-id="54ddf-195">Dostawcy funkcji aplikacji badają części aplikacji i udostępniają funkcje dla tych części.</span><span class="sxs-lookup"><span data-stu-id="54ddf-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="54ddf-196">Istnieją Wbudowani dostawcy funkcji dla następujących funkcji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="54ddf-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="54ddf-197">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="54ddf-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="54ddf-198">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="54ddf-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="54ddf-199">Wyświetl składniki</span><span class="sxs-lookup"><span data-stu-id="54ddf-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="54ddf-200">Dostawcy funkcji dziedziczą z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , gdzie `T` jest typem funkcji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="54ddf-201">Dostawców funkcji można zaimplementować dla dowolnego z wcześniej wymienionych typów funkcji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="54ddf-202">Kolejność dostawców funkcji w programie `ApplicationPartManager.FeatureProviders` może mieć wpływ na zachowanie.</span><span class="sxs-lookup"><span data-stu-id="54ddf-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="54ddf-203">Później dodani dostawcy mogą reagować na akcje podejmowane przez wcześniej dodanych dostawców.</span><span class="sxs-lookup"><span data-stu-id="54ddf-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="54ddf-204">Wyświetlanie dostępnych funkcji</span><span class="sxs-lookup"><span data-stu-id="54ddf-204">Display available features</span></span>

<span data-ttu-id="54ddf-205">Funkcje dostępne dla aplikacji można wyliczyć, żądając od `ApplicationPartManager` [iniekcji zależności](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="54ddf-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="54ddf-206">[Przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) używa powyższego kodu, aby wyświetlić funkcje aplikacji:</span><span class="sxs-lookup"><span data-stu-id="54ddf-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

## <a name="discovery-in-application-parts"></a><span data-ttu-id="54ddf-207">Odnajdywanie w częściach aplikacji</span><span class="sxs-lookup"><span data-stu-id="54ddf-207">Discovery in application parts</span></span>

<span data-ttu-id="54ddf-208">Błędy HTTP 404 nie są rzadko stosowane podczas tworzenia przy użyciu części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="54ddf-209">Te błędy są zwykle spowodowane brakiem zasadniczego wymagania w zakresie odnajdywania części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54ddf-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="54ddf-210">Jeśli aplikacja zwróci błąd HTTP 404, sprawdź, czy zostały spełnione następujące wymagania:</span><span class="sxs-lookup"><span data-stu-id="54ddf-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="54ddf-211">`applicationName`Ustawienie musi być ustawione na zestaw główny używany do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="54ddf-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="54ddf-212">Zestaw główny używany do odnajdywania jest zwykle zestawem punktów wejścia.</span><span class="sxs-lookup"><span data-stu-id="54ddf-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="54ddf-213">Zestaw główny musi mieć odwołanie do części używanych do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="54ddf-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="54ddf-214">Odwołanie może być bezpośrednie lub przechodnie.</span><span class="sxs-lookup"><span data-stu-id="54ddf-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="54ddf-215">Zestaw główny musi odwoływać się do zestawu SDK sieci Web.</span><span class="sxs-lookup"><span data-stu-id="54ddf-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="54ddf-216">Struktura ASP.NET Core ma niestandardową logikę kompilacji, która oznacza atrybuty w zestawie głównym, które są używane do odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="54ddf-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
