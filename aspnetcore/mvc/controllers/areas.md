---
title: Obszary w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak obszary są funkcją ASP.NET MVC służącą do organizowania powiązanych funkcji w grupie jako oddzielnej przestrzeni nazw (dla routingu) i struktury folderów (dla widoków).
ms.author: riande
ms.date: 03/21/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/areas
ms.openlocfilehash: f4471f54e92b940ecb0eabab82f03e0c77a0509f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406410"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="cdfae-103">Obszary w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdfae-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="cdfae-104">Autorzy [Dhananjay Kumara](https://twitter.com/debug_mode) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cdfae-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cdfae-105">Obszary są funkcją ASP.NET używaną do organizowania powiązanych funkcji w grupie jako oddzielnej:</span><span class="sxs-lookup"><span data-stu-id="cdfae-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="cdfae-106">Przestrzeń nazw dla routingu.</span><span class="sxs-lookup"><span data-stu-id="cdfae-106">Namespace for routing.</span></span>
* <span data-ttu-id="cdfae-107">Struktura folderów dla widoków i Razor stron.</span><span class="sxs-lookup"><span data-stu-id="cdfae-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="cdfae-108">Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie innego parametru trasy, `area` do `controller` i `action` lub Razor strony `page` .</span><span class="sxs-lookup"><span data-stu-id="cdfae-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="cdfae-109">Obszary umożliwiają dzielenie aplikacji sieci Web na ASP.NET Core do mniejszych grup funkcjonalnych, z których każdy ma własny zestaw Razor stron, kontrolerów, widoków i modeli.</span><span class="sxs-lookup"><span data-stu-id="cdfae-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="cdfae-110">Obszar jest efektywnie strukturą wewnątrz aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="cdfae-111">W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Pages, model, Controller i View, są przechowywane w różnych folderach.</span><span class="sxs-lookup"><span data-stu-id="cdfae-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="cdfae-112">Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa, aby utworzyć relację między tymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="cdfae-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="cdfae-113">W przypadku dużej aplikacji warto podzielić aplikację na oddzielne obszary wysokiego poziomu funkcji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="cdfae-114">Na przykład aplikacja handlu elektronicznego z wieloma jednostkami biznesowymi, takimi jak wyewidencjonowywanie, rozliczenia i wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="cdfae-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="cdfae-115">Każda z tych jednostek ma własny obszar, który zawiera widoki, kontrolery, Razor strony i modele.</span><span class="sxs-lookup"><span data-stu-id="cdfae-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="cdfae-116">Rozważ użycie obszarów w projekcie, gdy:</span><span class="sxs-lookup"><span data-stu-id="cdfae-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="cdfae-117">Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie oddzielić.</span><span class="sxs-lookup"><span data-stu-id="cdfae-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="cdfae-118">Chcesz podzielić aplikację na partycje, tak aby każdy obszar funkcjonalny mógł działać niezależnie.</span><span class="sxs-lookup"><span data-stu-id="cdfae-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="cdfae-119">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cdfae-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="cdfae-120">Przykład pobierania zawiera podstawową aplikację do testowania obszarów.</span><span class="sxs-lookup"><span data-stu-id="cdfae-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="cdfae-121">Jeśli używasz Razor stron, zobacz [obszary ze Razor stronami](#areas-with-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="cdfae-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="cdfae-122">Obszary dla kontrolerów z widokami</span><span class="sxs-lookup"><span data-stu-id="cdfae-122">Areas for controllers with views</span></span>

<span data-ttu-id="cdfae-123">Typowy ASP.NET Core aplikacja internetowa korzystająca z obszarów, kontrolerów i widoków zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="cdfae-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="cdfae-124">[Struktura folderów obszaru](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="cdfae-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="cdfae-125">Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:</span><span class="sxs-lookup"><span data-stu-id="cdfae-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="cdfae-126">[Trasa obszaru dodana do uruchamiania](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="cdfae-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="cdfae-127">Struktura folderów obszaru</span><span class="sxs-lookup"><span data-stu-id="cdfae-127">Area folder structure</span></span>

<span data-ttu-id="cdfae-128">Weź pod uwagę aplikację, która ma dwie grupy logiczne, *produkty* i *usługi*.</span><span class="sxs-lookup"><span data-stu-id="cdfae-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="cdfae-129">Przy użyciu obszarów struktura folderów będzie wyglądać podobnie do następujących:</span><span class="sxs-lookup"><span data-stu-id="cdfae-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="cdfae-130">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="cdfae-130">Project name</span></span>
  * <span data-ttu-id="cdfae-131">Obszary</span><span class="sxs-lookup"><span data-stu-id="cdfae-131">Areas</span></span>
    * <span data-ttu-id="cdfae-132">Produkty</span><span class="sxs-lookup"><span data-stu-id="cdfae-132">Products</span></span>
      * <span data-ttu-id="cdfae-133">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="cdfae-133">Controllers</span></span>
        * <span data-ttu-id="cdfae-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="cdfae-134">HomeController.cs</span></span>
        * <span data-ttu-id="cdfae-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="cdfae-135">ManageController.cs</span></span>
      * <span data-ttu-id="cdfae-136">Widoki</span><span class="sxs-lookup"><span data-stu-id="cdfae-136">Views</span></span>
        * <span data-ttu-id="cdfae-137">Strona główna</span><span class="sxs-lookup"><span data-stu-id="cdfae-137">Home</span></span>
          * <span data-ttu-id="cdfae-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-138">Index.cshtml</span></span>
        * <span data-ttu-id="cdfae-139">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="cdfae-139">Manage</span></span>
          * <span data-ttu-id="cdfae-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-140">Index.cshtml</span></span>
          * <span data-ttu-id="cdfae-141">About. cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-141">About.cshtml</span></span>
    * <span data-ttu-id="cdfae-142">Usługi</span><span class="sxs-lookup"><span data-stu-id="cdfae-142">Services</span></span>
      * <span data-ttu-id="cdfae-143">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="cdfae-143">Controllers</span></span>
        * <span data-ttu-id="cdfae-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="cdfae-144">HomeController.cs</span></span>
      * <span data-ttu-id="cdfae-145">Widoki</span><span class="sxs-lookup"><span data-stu-id="cdfae-145">Views</span></span>
        * <span data-ttu-id="cdfae-146">Strona główna</span><span class="sxs-lookup"><span data-stu-id="cdfae-146">Home</span></span>
          * <span data-ttu-id="cdfae-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-147">Index.cshtml</span></span>

<span data-ttu-id="cdfae-148">Chociaż poprzedni układ jest typowy w przypadku korzystania z obszarów, do korzystania z tej struktury folderów są wymagane tylko pliki widoku.</span><span class="sxs-lookup"><span data-stu-id="cdfae-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="cdfae-149">Wyświetl wyszukiwania odnajdywania dla zgodnego pliku widoku obszaru w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="cdfae-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="cdfae-150">Skojarz kontroler z obszarem</span><span class="sxs-lookup"><span data-stu-id="cdfae-150">Associate the controller with an Area</span></span>

<span data-ttu-id="cdfae-151">Kontrolery obszaru są oznaczone atrybutem [ &lbrack; obszaru &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="cdfae-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="cdfae-152">Dodaj trasę obszaru</span><span class="sxs-lookup"><span data-stu-id="cdfae-152">Add Area route</span></span>

<span data-ttu-id="cdfae-153">Trasy obszaru zwykle używają [konwencjonalnego routingu](xref:mvc/controllers/routing#cr) , a nie [routingu atrybutu](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="cdfae-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="cdfae-154">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="cdfae-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="cdfae-155">Ogólnie rzecz biorąc, trasy z obszarami należy umieścić wcześniej w tabeli tras, ponieważ są one bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="cdfae-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="cdfae-156">`{area:...}`może służyć jako token w szablonach tras, jeśli przestrzeń adresów URL jest jednolita dla wszystkich obszarów:</span><span class="sxs-lookup"><span data-stu-id="cdfae-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="cdfae-157">W poprzednim kodzie, `exists` stosuje ograniczenie, które musi być zgodne z obszarem.</span><span class="sxs-lookup"><span data-stu-id="cdfae-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="cdfae-158">Używanie `{area:...}` z `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="cdfae-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="cdfae-159">To najmniej skomplikowany mechanizm dodawania routingu do obszarów.</span><span class="sxs-lookup"><span data-stu-id="cdfae-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="cdfae-160">Dopasowuje wszystkie kontrolery z `[Area("Area name")]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="cdfae-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="cdfae-161">Poniższy kod używa <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> do tworzenia dwóch nazwanych tras obszaru:</span><span class="sxs-lookup"><span data-stu-id="cdfae-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="cdfae-162">Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="cdfae-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="cdfae-163">Generowanie linków z obszarami MVC</span><span class="sxs-lookup"><span data-stu-id="cdfae-163">Link generation with MVC areas</span></span>

<span data-ttu-id="cdfae-164">Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) pokazuje generowanie linków z określonym obszarem:</span><span class="sxs-lookup"><span data-stu-id="cdfae-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="cdfae-165">Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający:</span><span class="sxs-lookup"><span data-stu-id="cdfae-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="cdfae-166">Poprzednie linki.</span><span class="sxs-lookup"><span data-stu-id="cdfae-166">The preceding links.</span></span>
* <span data-ttu-id="cdfae-167">Linki podobne do poprzedniego Except `area` nie zostały określone.</span><span class="sxs-lookup"><span data-stu-id="cdfae-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="cdfae-168">Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki.</span><span class="sxs-lookup"><span data-stu-id="cdfae-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="cdfae-169">Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy jest przywoływany ze strony w tym samym obszarze i kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="cdfae-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="cdfae-170">Gdy nie określono obszaru lub kontrolera, routing zależy od wartości [otoczenia](xref:mvc/controllers/routing#ambient) .</span><span class="sxs-lookup"><span data-stu-id="cdfae-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="cdfae-171">Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza.</span><span class="sxs-lookup"><span data-stu-id="cdfae-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="cdfae-172">W wielu przypadkach dla przykładowej aplikacji użycie wartości otoczenia powoduje wygenerowanie nieprawidłowych linków ze znacznikiem, które nie określa obszaru.</span><span class="sxs-lookup"><span data-stu-id="cdfae-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="cdfae-173">Aby uzyskać więcej informacji, zobacz [routing do kontrolera akcji](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="cdfae-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="cdfae-174">Układ współużytkowany dla obszarów korzystających z pliku _ViewStart. cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="cdfae-175">Aby udostępnić wspólny układ całej aplikacji, Zachowaj *_ViewStart. cshtml* w [folderze głównym aplikacji](#arf).</span><span class="sxs-lookup"><span data-stu-id="cdfae-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="cdfae-176">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="cdfae-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="cdfae-177">Folder główny aplikacji</span><span class="sxs-lookup"><span data-stu-id="cdfae-177">Application root folder</span></span>

<span data-ttu-id="cdfae-178">Folder główny aplikacji jest folderem zawierającym *Startup.cs* w aplikacji internetowej utworzonej za pomocą szablonów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdfae-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="cdfae-179">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="cdfae-180">*/Views/_ViewImports. cshtml*, dla MVC i */Pages/_ViewImports. cshtml* dla Razor stron nie są importowane do widoków w obszarach.</span><span class="sxs-lookup"><span data-stu-id="cdfae-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="cdfae-181">Użyj jednej z następujących metod, aby udostępnić widok Imports do wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="cdfae-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="cdfae-182">Dodaj *_ViewImports. cshtml* do [folderu głównego aplikacji](#arf).</span><span class="sxs-lookup"><span data-stu-id="cdfae-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="cdfae-183">*_ViewImports. cshtml* w folderze głównym aplikacji zostanie zastosowane do wszystkich widoków w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="cdfae-184">Skopiuj plik *_ViewImports. cshtml* do odpowiedniego folderu widoku w obszarze obszary.</span><span class="sxs-lookup"><span data-stu-id="cdfae-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="cdfae-185">Plik *_ViewImports. cshtml* [zazwyczaj zawiera](xref:mvc/views/tag-helpers/intro) instrukcje Imports, `@using` , i `@inject` .</span><span class="sxs-lookup"><span data-stu-id="cdfae-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="cdfae-186">Aby uzyskać więcej informacji, zobacz [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="cdfae-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="cdfae-187">Zmień domyślny folder obszaru, w którym są przechowywane widoki</span><span class="sxs-lookup"><span data-stu-id="cdfae-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="cdfae-188">Poniższy kod zmienia domyślny folder obszaru z `"Areas"` na `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="cdfae-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="cdfae-189">Obszary ze Razor stronami</span><span class="sxs-lookup"><span data-stu-id="cdfae-189">Areas with Razor Pages</span></span>

<span data-ttu-id="cdfae-190">Obszary ze Razor stronami wymagają `Areas/<area name>/Pages` folderu w folderze głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="cdfae-191">Następująca struktura folderów jest używana z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span><span class="sxs-lookup"><span data-stu-id="cdfae-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="cdfae-192">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="cdfae-192">Project name</span></span>
  * <span data-ttu-id="cdfae-193">Obszary</span><span class="sxs-lookup"><span data-stu-id="cdfae-193">Areas</span></span>
    * <span data-ttu-id="cdfae-194">Produkty</span><span class="sxs-lookup"><span data-stu-id="cdfae-194">Products</span></span>
      * <span data-ttu-id="cdfae-195">Strony</span><span class="sxs-lookup"><span data-stu-id="cdfae-195">Pages</span></span>
        * <span data-ttu-id="cdfae-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="cdfae-196">_ViewImports</span></span>
        * <span data-ttu-id="cdfae-197">Informacje</span><span class="sxs-lookup"><span data-stu-id="cdfae-197">About</span></span>
        * <span data-ttu-id="cdfae-198">Indeks</span><span class="sxs-lookup"><span data-stu-id="cdfae-198">Index</span></span>
    * <span data-ttu-id="cdfae-199">Usługi</span><span class="sxs-lookup"><span data-stu-id="cdfae-199">Services</span></span>
      * <span data-ttu-id="cdfae-200">Strony</span><span class="sxs-lookup"><span data-stu-id="cdfae-200">Pages</span></span>
        * <span data-ttu-id="cdfae-201">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="cdfae-201">Manage</span></span>
          * <span data-ttu-id="cdfae-202">Informacje</span><span class="sxs-lookup"><span data-stu-id="cdfae-202">About</span></span>
          * <span data-ttu-id="cdfae-203">Indeks</span><span class="sxs-lookup"><span data-stu-id="cdfae-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="cdfae-204">Generowanie linków przy użyciu Razor stron i obszarów</span><span class="sxs-lookup"><span data-stu-id="cdfae-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="cdfae-205">Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje Generowanie łącza z określonym obszarem (na przykład `asp-area="Products"` ):</span><span class="sxs-lookup"><span data-stu-id="cdfae-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="cdfae-206">Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający poprzednie linki i te same linki bez określania obszaru.</span><span class="sxs-lookup"><span data-stu-id="cdfae-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="cdfae-207">Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki.</span><span class="sxs-lookup"><span data-stu-id="cdfae-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="cdfae-208">Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy są przywoływane ze strony w tym samym obszarze.</span><span class="sxs-lookup"><span data-stu-id="cdfae-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="cdfae-209">Gdy obszar nie zostanie określony, routing zależy od wartości *otoczenia* .</span><span class="sxs-lookup"><span data-stu-id="cdfae-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="cdfae-210">Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza.</span><span class="sxs-lookup"><span data-stu-id="cdfae-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="cdfae-211">W wielu przypadkach dla przykładowej aplikacji korzystanie z wartości otoczenia powoduje wygenerowanie nieprawidłowych linków.</span><span class="sxs-lookup"><span data-stu-id="cdfae-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="cdfae-212">Rozważmy na przykład linki wygenerowane na podstawie następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="cdfae-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="cdfae-213">Dla poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="cdfae-213">For the preceding code:</span></span>

* <span data-ttu-id="cdfae-214">Link wygenerowany z `<a asp-page="/Manage/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze.</span><span class="sxs-lookup"><span data-stu-id="cdfae-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="cdfae-215">Na przykład, `/Services/Manage/` , `/Services/Manage/Index` lub `/Services/Manage/About` .</span><span class="sxs-lookup"><span data-stu-id="cdfae-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="cdfae-216">Link wygenerowany z `<a asp-page="/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `/Home` .</span><span class="sxs-lookup"><span data-stu-id="cdfae-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="cdfae-217">Kod pochodzi z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="cdfae-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="cdfae-218">Importowanie przestrzeni nazw i pomocników tagów przy użyciu pliku _ViewImports</span><span class="sxs-lookup"><span data-stu-id="cdfae-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="cdfae-219">Plik *_ViewImports. cshtml* można dodać do każdego folderu *stron* obszaru, aby zaimportować przestrzeń nazw i Tagi pomocników do każdej Razor strony w folderze.</span><span class="sxs-lookup"><span data-stu-id="cdfae-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="cdfae-220">Weź pod uwagę obszar *usług* przykładowego kodu, który nie zawiera pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cdfae-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="cdfae-221">Następujące znaczniki przedstawiają stronę */Services/Manage/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="cdfae-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="cdfae-222">W powyższym znaczniku:</span><span class="sxs-lookup"><span data-stu-id="cdfae-222">In the preceding markup:</span></span>

* <span data-ttu-id="cdfae-223">W pełni kwalifikowana nazwa domeny musi zostać użyta do określenia modelu ( `@model RPareas.Areas.Services.Pages.Manage.AboutModel` ).</span><span class="sxs-lookup"><span data-stu-id="cdfae-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="cdfae-224">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) są włączani przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="cdfae-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="cdfae-225">W przykładowym pobieranym obszarze produkty znajdują się następujące *_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cdfae-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="cdfae-226">Następujące znaczniki przedstawiają stronę */Products/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="cdfae-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="cdfae-227">W poprzednim pliku przestrzeń nazw i `@addTagHelper` dyrektywa są importowane do pliku przez *obszary/produkty/strony/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cdfae-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="cdfae-228">Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika tagów](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="cdfae-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="cdfae-229">Układ współużytkowany dla Razor obszarów stron</span><span class="sxs-lookup"><span data-stu-id="cdfae-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="cdfae-230">Aby udostępnić wspólny układ całej aplikacji, Przenieś *_ViewStart. cshtml* do folderu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="cdfae-231">Publikowanie obszarów</span><span class="sxs-lookup"><span data-stu-id="cdfae-231">Publishing Areas</span></span>

<span data-ttu-id="cdfae-232">Wszystkie pliki \*. cshtml i pliki znajdujące się w katalogu *wwwroot* są publikowane w danych wyjściowych `<Project Sdk="Microsoft.NET.Sdk.Web">` , gdy są zawarte w pliku \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="cdfae-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cdfae-233">Obszary są funkcją ASP.NET używaną do organizowania powiązanych funkcji w grupie jako oddzielnej przestrzeni nazw (dla routingu) i struktury folderów (dla widoków).</span><span class="sxs-lookup"><span data-stu-id="cdfae-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="cdfae-234">Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie innego parametru trasy, `area` do `controller` i `action` lub Razor strony `page` .</span><span class="sxs-lookup"><span data-stu-id="cdfae-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="cdfae-235">Obszary umożliwiają dzielenie aplikacji sieci Web na ASP.NET Core do mniejszych grup funkcjonalnych, z których każdy ma własny zestaw Razor stron, kontrolerów, widoków i modeli.</span><span class="sxs-lookup"><span data-stu-id="cdfae-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="cdfae-236">Obszar jest efektywnie strukturą wewnątrz aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="cdfae-237">W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Pages, model, Controller i View, są przechowywane w różnych folderach.</span><span class="sxs-lookup"><span data-stu-id="cdfae-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="cdfae-238">Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa, aby utworzyć relację między tymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="cdfae-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="cdfae-239">W przypadku dużej aplikacji warto podzielić aplikację na oddzielne obszary wysokiego poziomu funkcji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="cdfae-240">Na przykład aplikacja handlu elektronicznego z wieloma jednostkami biznesowymi, takimi jak wyewidencjonowywanie, rozliczenia i wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="cdfae-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="cdfae-241">Każda z tych jednostek ma własny obszar, który zawiera widoki, kontrolery, Razor strony i modele.</span><span class="sxs-lookup"><span data-stu-id="cdfae-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="cdfae-242">Rozważ użycie obszarów w projekcie, gdy:</span><span class="sxs-lookup"><span data-stu-id="cdfae-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="cdfae-243">Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie oddzielić.</span><span class="sxs-lookup"><span data-stu-id="cdfae-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="cdfae-244">Chcesz podzielić aplikację na partycje, tak aby każdy obszar funkcjonalny mógł działać niezależnie.</span><span class="sxs-lookup"><span data-stu-id="cdfae-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="cdfae-245">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cdfae-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="cdfae-246">Przykład pobierania zawiera podstawową aplikację do testowania obszarów.</span><span class="sxs-lookup"><span data-stu-id="cdfae-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="cdfae-247">Jeśli używasz Razor stron, zobacz [obszary ze Razor stronami](#areas-with-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="cdfae-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="cdfae-248">Obszary dla kontrolerów z widokami</span><span class="sxs-lookup"><span data-stu-id="cdfae-248">Areas for controllers with views</span></span>

<span data-ttu-id="cdfae-249">Typowy ASP.NET Core aplikacja internetowa korzystająca z obszarów, kontrolerów i widoków zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="cdfae-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="cdfae-250">[Struktura folderów obszaru](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="cdfae-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="cdfae-251">Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:</span><span class="sxs-lookup"><span data-stu-id="cdfae-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="cdfae-252">[Trasa obszaru dodana do uruchamiania](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="cdfae-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="cdfae-253">Struktura folderów obszaru</span><span class="sxs-lookup"><span data-stu-id="cdfae-253">Area folder structure</span></span>

<span data-ttu-id="cdfae-254">Weź pod uwagę aplikację, która ma dwie grupy logiczne, *produkty* i *usługi*.</span><span class="sxs-lookup"><span data-stu-id="cdfae-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="cdfae-255">Przy użyciu obszarów struktura folderów będzie wyglądać podobnie do następujących:</span><span class="sxs-lookup"><span data-stu-id="cdfae-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="cdfae-256">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="cdfae-256">Project name</span></span>
  * <span data-ttu-id="cdfae-257">Obszary</span><span class="sxs-lookup"><span data-stu-id="cdfae-257">Areas</span></span>
    * <span data-ttu-id="cdfae-258">Produkty</span><span class="sxs-lookup"><span data-stu-id="cdfae-258">Products</span></span>
      * <span data-ttu-id="cdfae-259">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="cdfae-259">Controllers</span></span>
        * <span data-ttu-id="cdfae-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="cdfae-260">HomeController.cs</span></span>
        * <span data-ttu-id="cdfae-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="cdfae-261">ManageController.cs</span></span>
      * <span data-ttu-id="cdfae-262">Widoki</span><span class="sxs-lookup"><span data-stu-id="cdfae-262">Views</span></span>
        * <span data-ttu-id="cdfae-263">Strona główna</span><span class="sxs-lookup"><span data-stu-id="cdfae-263">Home</span></span>
          * <span data-ttu-id="cdfae-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-264">Index.cshtml</span></span>
        * <span data-ttu-id="cdfae-265">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="cdfae-265">Manage</span></span>
          * <span data-ttu-id="cdfae-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-266">Index.cshtml</span></span>
          * <span data-ttu-id="cdfae-267">About. cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-267">About.cshtml</span></span>
    * <span data-ttu-id="cdfae-268">Usługi</span><span class="sxs-lookup"><span data-stu-id="cdfae-268">Services</span></span>
      * <span data-ttu-id="cdfae-269">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="cdfae-269">Controllers</span></span>
        * <span data-ttu-id="cdfae-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="cdfae-270">HomeController.cs</span></span>
      * <span data-ttu-id="cdfae-271">Widoki</span><span class="sxs-lookup"><span data-stu-id="cdfae-271">Views</span></span>
        * <span data-ttu-id="cdfae-272">Strona główna</span><span class="sxs-lookup"><span data-stu-id="cdfae-272">Home</span></span>
          * <span data-ttu-id="cdfae-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-273">Index.cshtml</span></span>

<span data-ttu-id="cdfae-274">Chociaż poprzedni układ jest typowy w przypadku korzystania z obszarów, do korzystania z tej struktury folderów są wymagane tylko pliki widoku.</span><span class="sxs-lookup"><span data-stu-id="cdfae-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="cdfae-275">Wyświetl wyszukiwania odnajdywania dla zgodnego pliku widoku obszaru w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="cdfae-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="cdfae-276">Skojarz kontroler z obszarem</span><span class="sxs-lookup"><span data-stu-id="cdfae-276">Associate the controller with an Area</span></span>

<span data-ttu-id="cdfae-277">Kontrolery obszaru są oznaczone atrybutem [ &lbrack; obszaru &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="cdfae-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="cdfae-278">Dodaj trasę obszaru</span><span class="sxs-lookup"><span data-stu-id="cdfae-278">Add Area route</span></span>

<span data-ttu-id="cdfae-279">Trasy obszaru zwykle używają konwencjonalnego routingu, a nie routingu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="cdfae-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="cdfae-280">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="cdfae-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="cdfae-281">Ogólnie rzecz biorąc, trasy z obszarami należy umieścić wcześniej w tabeli tras, ponieważ są one bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="cdfae-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="cdfae-282">`{area:...}`może służyć jako token w szablonach tras, jeśli przestrzeń adresów URL jest jednolita dla wszystkich obszarów:</span><span class="sxs-lookup"><span data-stu-id="cdfae-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="cdfae-283">W poprzednim kodzie, `exists` stosuje ograniczenie, które musi być zgodne z obszarem.</span><span class="sxs-lookup"><span data-stu-id="cdfae-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="cdfae-284">Korzystanie z programu `{area:...}` to najmniej skomplikowany mechanizm dodawania routingu do obszarów.</span><span class="sxs-lookup"><span data-stu-id="cdfae-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="cdfae-285">Poniższy kod używa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> do tworzenia dwóch nazwanych tras obszaru:</span><span class="sxs-lookup"><span data-stu-id="cdfae-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="cdfae-286">W przypadku korzystania `MapAreaRoute` z programu z ASP.NET Core 2,2, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/7772)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="cdfae-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="cdfae-287">Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="cdfae-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="cdfae-288">Generowanie linków z obszarami MVC</span><span class="sxs-lookup"><span data-stu-id="cdfae-288">Link generation with MVC areas</span></span>

<span data-ttu-id="cdfae-289">Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) pokazuje generowanie linków z określonym obszarem:</span><span class="sxs-lookup"><span data-stu-id="cdfae-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="cdfae-290">Linki generowane za pomocą powyższego kodu są prawidłowe w dowolnym miejscu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="cdfae-291">Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający poprzednie linki i te same linki bez określania obszaru.</span><span class="sxs-lookup"><span data-stu-id="cdfae-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="cdfae-292">Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki.</span><span class="sxs-lookup"><span data-stu-id="cdfae-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="cdfae-293">Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy jest przywoływany ze strony w tym samym obszarze i kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="cdfae-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="cdfae-294">Gdy nie określono obszaru lub kontrolera, routing zależy od wartości *otoczenia* .</span><span class="sxs-lookup"><span data-stu-id="cdfae-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="cdfae-295">Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza.</span><span class="sxs-lookup"><span data-stu-id="cdfae-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="cdfae-296">W wielu przypadkach dla przykładowej aplikacji korzystanie z wartości otoczenia powoduje wygenerowanie nieprawidłowych linków.</span><span class="sxs-lookup"><span data-stu-id="cdfae-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="cdfae-297">Aby uzyskać więcej informacji, zobacz [routing do kontrolera akcji](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="cdfae-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="cdfae-298">Układ współużytkowany dla obszarów korzystających z pliku _ViewStart. cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="cdfae-299">Aby udostępnić wspólny układ całej aplikacji, Przenieś *_ViewStart. cshtml* do folderu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="cdfae-300">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="cdfae-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="cdfae-301">W swojej lokalizacji standardowej */Views/_ViewImports. cshtml* nie ma zastosowania do obszarów.</span><span class="sxs-lookup"><span data-stu-id="cdfae-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="cdfae-302">Aby użyć wspólnych [pomocników tagów](xref:mvc/views/tag-helpers/intro), `@using` lub `@inject` w Twoim regionie, upewnij się, że odpowiedni plik *_ViewImports. cshtml* [ma zastosowanie do widoków obszaru](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="cdfae-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="cdfae-303">Jeśli chcesz mieć takie samo zachowanie we wszystkich widokach, Przenieś */Views/_ViewImports. cshtml* do katalogu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="cdfae-304">Zmień domyślny folder obszaru, w którym są przechowywane widoki</span><span class="sxs-lookup"><span data-stu-id="cdfae-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="cdfae-305">Poniższy kod zmienia domyślny folder obszaru z `"Areas"` na `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="cdfae-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="cdfae-306">Obszary ze Razor stronami</span><span class="sxs-lookup"><span data-stu-id="cdfae-306">Areas with Razor Pages</span></span>

<span data-ttu-id="cdfae-307">Obszary ze Razor stronami wymagają `Areas/<area name>/Pages` folderu w folderze głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="cdfae-308">Następująca struktura folderów jest używana z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span><span class="sxs-lookup"><span data-stu-id="cdfae-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="cdfae-309">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="cdfae-309">Project name</span></span>
  * <span data-ttu-id="cdfae-310">Obszary</span><span class="sxs-lookup"><span data-stu-id="cdfae-310">Areas</span></span>
    * <span data-ttu-id="cdfae-311">Produkty</span><span class="sxs-lookup"><span data-stu-id="cdfae-311">Products</span></span>
      * <span data-ttu-id="cdfae-312">Strony</span><span class="sxs-lookup"><span data-stu-id="cdfae-312">Pages</span></span>
        * <span data-ttu-id="cdfae-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="cdfae-313">_ViewImports</span></span>
        * <span data-ttu-id="cdfae-314">Informacje</span><span class="sxs-lookup"><span data-stu-id="cdfae-314">About</span></span>
        * <span data-ttu-id="cdfae-315">Indeks</span><span class="sxs-lookup"><span data-stu-id="cdfae-315">Index</span></span>
    * <span data-ttu-id="cdfae-316">Usługi</span><span class="sxs-lookup"><span data-stu-id="cdfae-316">Services</span></span>
      * <span data-ttu-id="cdfae-317">Strony</span><span class="sxs-lookup"><span data-stu-id="cdfae-317">Pages</span></span>
        * <span data-ttu-id="cdfae-318">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="cdfae-318">Manage</span></span>
          * <span data-ttu-id="cdfae-319">Informacje</span><span class="sxs-lookup"><span data-stu-id="cdfae-319">About</span></span>
          * <span data-ttu-id="cdfae-320">Indeks</span><span class="sxs-lookup"><span data-stu-id="cdfae-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="cdfae-321">Generowanie linków przy użyciu Razor stron i obszarów</span><span class="sxs-lookup"><span data-stu-id="cdfae-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="cdfae-322">Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje Generowanie łącza z określonym obszarem (na przykład `asp-area="Products"` ):</span><span class="sxs-lookup"><span data-stu-id="cdfae-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="cdfae-323">Linki generowane za pomocą powyższego kodu są prawidłowe w dowolnym miejscu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="cdfae-324">Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający poprzednie linki i te same linki bez określania obszaru.</span><span class="sxs-lookup"><span data-stu-id="cdfae-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="cdfae-325">Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki.</span><span class="sxs-lookup"><span data-stu-id="cdfae-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="cdfae-326">Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy są przywoływane ze strony w tym samym obszarze.</span><span class="sxs-lookup"><span data-stu-id="cdfae-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="cdfae-327">Gdy obszar nie zostanie określony, routing zależy od wartości *otoczenia* .</span><span class="sxs-lookup"><span data-stu-id="cdfae-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="cdfae-328">Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza.</span><span class="sxs-lookup"><span data-stu-id="cdfae-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="cdfae-329">W wielu przypadkach dla przykładowej aplikacji korzystanie z wartości otoczenia powoduje wygenerowanie nieprawidłowych linków.</span><span class="sxs-lookup"><span data-stu-id="cdfae-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="cdfae-330">Rozważmy na przykład linki wygenerowane na podstawie następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="cdfae-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="cdfae-331">Dla poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="cdfae-331">For the preceding code:</span></span>

* <span data-ttu-id="cdfae-332">Link wygenerowany z `<a asp-page="/Manage/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze.</span><span class="sxs-lookup"><span data-stu-id="cdfae-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="cdfae-333">Na przykład, `/Services/Manage/` , `/Services/Manage/Index` lub `/Services/Manage/About` .</span><span class="sxs-lookup"><span data-stu-id="cdfae-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="cdfae-334">Link wygenerowany z `<a asp-page="/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `/Home` .</span><span class="sxs-lookup"><span data-stu-id="cdfae-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="cdfae-335">Kod pochodzi z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="cdfae-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="cdfae-336">Importowanie przestrzeni nazw i pomocników tagów przy użyciu pliku _ViewImports</span><span class="sxs-lookup"><span data-stu-id="cdfae-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="cdfae-337">Plik *_ViewImports. cshtml* można dodać do każdego folderu *stron* obszaru, aby zaimportować przestrzeń nazw i Tagi pomocników do każdej Razor strony w folderze.</span><span class="sxs-lookup"><span data-stu-id="cdfae-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="cdfae-338">Weź pod uwagę obszar *usług* przykładowego kodu, który nie zawiera pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cdfae-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="cdfae-339">Następujące znaczniki przedstawiają stronę */Services/Manage/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="cdfae-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="cdfae-340">W powyższym znaczniku:</span><span class="sxs-lookup"><span data-stu-id="cdfae-340">In the preceding markup:</span></span>

* <span data-ttu-id="cdfae-341">W pełni kwalifikowana nazwa domeny musi zostać użyta do określenia modelu ( `@model RPareas.Areas.Services.Pages.Manage.AboutModel` ).</span><span class="sxs-lookup"><span data-stu-id="cdfae-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="cdfae-342">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) są włączani przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="cdfae-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="cdfae-343">W przykładowym pobieranym obszarze produkty znajdują się następujące *_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cdfae-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="cdfae-344">Następujące znaczniki przedstawiają stronę */Products/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="cdfae-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="cdfae-345">W poprzednim pliku przestrzeń nazw i `@addTagHelper` dyrektywa są importowane do pliku przez *obszary/produkty/strony/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cdfae-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="cdfae-346">Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika tagów](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="cdfae-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="cdfae-347">Układ współużytkowany dla Razor obszarów stron</span><span class="sxs-lookup"><span data-stu-id="cdfae-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="cdfae-348">Aby udostępnić wspólny układ całej aplikacji, Przenieś *_ViewStart. cshtml* do folderu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdfae-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="cdfae-349">Publikowanie obszarów</span><span class="sxs-lookup"><span data-stu-id="cdfae-349">Publishing Areas</span></span>

<span data-ttu-id="cdfae-350">Wszystkie pliki \*. cshtml i pliki znajdujące się w katalogu *wwwroot* są publikowane w danych wyjściowych `<Project Sdk="Microsoft.NET.Sdk.Web">` , gdy są zawarte w pliku \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="cdfae-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
