---
title: Obszary w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak obszary są funkcją ASP.NET MVC służącą do organizowania powiązanych funkcji w grupie jako oddzielnej przestrzeni nazw (dla routingu) i struktury folderów (dla widoków).
ms.author: riande
ms.date: 03/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/controllers/areas
ms.openlocfilehash: 42eec406813adce4d7edbc1ab66a1f689c4aca0e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053530"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="c2a5d-103">Obszary w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2a5d-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="c2a5d-104">Autorzy [Dhananjay Kumara](https://twitter.com/debug_mode) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c2a5d-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2a5d-105">Obszary są funkcją ASP.NET używaną do organizowania powiązanych funkcji w grupie jako oddzielnej:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="c2a5d-106">Przestrzeń nazw dla routingu.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-106">Namespace for routing.</span></span>
* <span data-ttu-id="c2a5d-107">Struktura folderów dla widoków i :::no-loc(Razor)::: stron.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-107">Folder structure for views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="c2a5d-108">Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie innego parametru trasy, `area` do `controller` i `action` lub :::no-loc(Razor)::: strony `page` .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="c2a5d-109">Obszary umożliwiają dzielenie aplikacji sieci Web na ASP.NET Core do mniejszych grup funkcjonalnych, z których każdy ma własny zestaw :::no-loc(Razor)::: stron, kontrolerów, widoków i modeli.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="c2a5d-110">Obszar jest efektywnie strukturą wewnątrz aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="c2a5d-111">W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Pages, model, Controller i View, są przechowywane w różnych folderach.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="c2a5d-112">Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa, aby utworzyć relację między tymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="c2a5d-113">W przypadku dużej aplikacji warto podzielić aplikację na oddzielne obszary wysokiego poziomu funkcji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="c2a5d-114">Na przykład aplikacja handlu elektronicznego z wieloma jednostkami biznesowymi, takimi jak wyewidencjonowywanie, rozliczenia i wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="c2a5d-115">Każda z tych jednostek ma własny obszar, który zawiera widoki, kontrolery, :::no-loc(Razor)::: strony i modele.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-115">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="c2a5d-116">Rozważ użycie obszarów w projekcie, gdy:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="c2a5d-117">Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie oddzielić.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="c2a5d-118">Chcesz podzielić aplikację na partycje, tak aby każdy obszar funkcjonalny mógł działać niezależnie.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="c2a5d-119">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="c2a5d-120">Przykład pobierania zawiera podstawową aplikację do testowania obszarów.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="c2a5d-121">Jeśli używasz :::no-loc(Razor)::: stron, zobacz [obszary ze :::no-loc(Razor)::: stronami](#areas-with-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-121">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="c2a5d-122">Obszary dla kontrolerów z widokami</span><span class="sxs-lookup"><span data-stu-id="c2a5d-122">Areas for controllers with views</span></span>

<span data-ttu-id="c2a5d-123">Typowy ASP.NET Core aplikacja internetowa korzystająca z obszarów, kontrolerów i widoków zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="c2a5d-124">[Struktura folderów obszaru](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="c2a5d-125">Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="c2a5d-126">[Trasa obszaru dodana do uruchamiania](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="c2a5d-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="c2a5d-127">Struktura folderów obszaru</span><span class="sxs-lookup"><span data-stu-id="c2a5d-127">Area folder structure</span></span>

<span data-ttu-id="c2a5d-128">Weź pod uwagę aplikację, która ma dwie grupy logiczne, *produkty* i *usługi* .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-128">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="c2a5d-129">Przy użyciu obszarów struktura folderów będzie wyglądać podobnie do następujących:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="c2a5d-130">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="c2a5d-130">Project name</span></span>
  * <span data-ttu-id="c2a5d-131">Obszary</span><span class="sxs-lookup"><span data-stu-id="c2a5d-131">Areas</span></span>
    * <span data-ttu-id="c2a5d-132">Produkty</span><span class="sxs-lookup"><span data-stu-id="c2a5d-132">Products</span></span>
      * <span data-ttu-id="c2a5d-133">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="c2a5d-133">Controllers</span></span>
        * <span data-ttu-id="c2a5d-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="c2a5d-134">HomeController.cs</span></span>
        * <span data-ttu-id="c2a5d-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="c2a5d-135">ManageController.cs</span></span>
      * <span data-ttu-id="c2a5d-136">Widoki</span><span class="sxs-lookup"><span data-stu-id="c2a5d-136">Views</span></span>
        * <span data-ttu-id="c2a5d-137">Strona główna</span><span class="sxs-lookup"><span data-stu-id="c2a5d-137">Home</span></span>
          * <span data-ttu-id="c2a5d-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-138">Index.cshtml</span></span>
        * <span data-ttu-id="c2a5d-139">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="c2a5d-139">Manage</span></span>
          * <span data-ttu-id="c2a5d-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-140">Index.cshtml</span></span>
          * <span data-ttu-id="c2a5d-141">About. cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-141">About.cshtml</span></span>
    * <span data-ttu-id="c2a5d-142">Usługi</span><span class="sxs-lookup"><span data-stu-id="c2a5d-142">Services</span></span>
      * <span data-ttu-id="c2a5d-143">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="c2a5d-143">Controllers</span></span>
        * <span data-ttu-id="c2a5d-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="c2a5d-144">HomeController.cs</span></span>
      * <span data-ttu-id="c2a5d-145">Widoki</span><span class="sxs-lookup"><span data-stu-id="c2a5d-145">Views</span></span>
        * <span data-ttu-id="c2a5d-146">Strona główna</span><span class="sxs-lookup"><span data-stu-id="c2a5d-146">Home</span></span>
          * <span data-ttu-id="c2a5d-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-147">Index.cshtml</span></span>

<span data-ttu-id="c2a5d-148">Chociaż poprzedni układ jest typowy w przypadku korzystania z obszarów, do korzystania z tej struktury folderów są wymagane tylko pliki widoku.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="c2a5d-149">Wyświetl wyszukiwania odnajdywania dla zgodnego pliku widoku obszaru w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="c2a5d-150">Skojarz kontroler z obszarem</span><span class="sxs-lookup"><span data-stu-id="c2a5d-150">Associate the controller with an Area</span></span>

<span data-ttu-id="c2a5d-151">Kontrolery obszaru są oznaczone atrybutem [ &lbrack; obszaru &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="c2a5d-152">Dodaj trasę obszaru</span><span class="sxs-lookup"><span data-stu-id="c2a5d-152">Add Area route</span></span>

<span data-ttu-id="c2a5d-153">Trasy obszaru zwykle używają  [konwencjonalnego routingu](xref:mvc/controllers/routing#cr) , a nie [routingu atrybutu](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="c2a5d-154">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="c2a5d-155">Ogólnie rzecz biorąc, trasy z obszarami należy umieścić wcześniej w tabeli tras, ponieważ są one bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="c2a5d-156">`{area:...}` może służyć jako token w szablonach tras, jeśli przestrzeń adresów URL jest jednolita dla wszystkich obszarów:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="c2a5d-157">W poprzednim kodzie, `exists` stosuje ograniczenie, które musi być zgodne z obszarem.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="c2a5d-158">Używanie `{area:...}` z `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="c2a5d-159">To najmniej skomplikowany mechanizm dodawania routingu do obszarów.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="c2a5d-160">Dopasowuje wszystkie kontrolery z `[Area("Area name")]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="c2a5d-161">Poniższy kod używa <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> do tworzenia dwóch nazwanych tras obszaru:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="c2a5d-162">Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="c2a5d-163">Generowanie linków z obszarami MVC</span><span class="sxs-lookup"><span data-stu-id="c2a5d-163">Link generation with MVC areas</span></span>

<span data-ttu-id="c2a5d-164">Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) pokazuje generowanie linków z określonym obszarem:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="c2a5d-165">Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="c2a5d-166">Poprzednie linki.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-166">The preceding links.</span></span>
* <span data-ttu-id="c2a5d-167">Linki podobne do poprzedniego Except `area` nie zostały określone.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="c2a5d-168">Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="c2a5d-169">Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy jest przywoływany ze strony w tym samym obszarze i kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="c2a5d-170">Gdy nie określono obszaru lub kontrolera, routing zależy od wartości [otoczenia](xref:mvc/controllers/routing#ambient) .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="c2a5d-171">Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="c2a5d-172">W wielu przypadkach dla przykładowej aplikacji użycie wartości otoczenia powoduje wygenerowanie nieprawidłowych linków ze znacznikiem, które nie określa obszaru.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="c2a5d-173">Aby uzyskać więcej informacji, zobacz [routing do kontrolera akcji](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="c2a5d-174">Układ współużytkowany dla obszarów korzystających z pliku _ViewStart. cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="c2a5d-175">Aby udostępnić wspólny układ całej aplikacji, Zachowaj *_ViewStart. cshtml* w [folderze głównym aplikacji](#arf).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="c2a5d-176">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="c2a5d-177">Folder główny aplikacji</span><span class="sxs-lookup"><span data-stu-id="c2a5d-177">Application root folder</span></span>

<span data-ttu-id="c2a5d-178">Folder główny aplikacji jest folderem zawierającym *Startup.cs* w aplikacji internetowej utworzonej za pomocą szablonów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="c2a5d-179">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="c2a5d-180">*/Views/_ViewImports. cshtml* , dla MVC i */Pages/_ViewImports. cshtml* dla :::no-loc(Razor)::: stron nie są importowane do widoków w obszarach.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-180">*/Views/_ViewImports.cshtml* , for MVC, and */Pages/_ViewImports.cshtml* for :::no-loc(Razor)::: Pages, is not imported to views in areas.</span></span> <span data-ttu-id="c2a5d-181">Użyj jednej z następujących metod, aby udostępnić widok Imports do wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="c2a5d-182">Dodaj *_ViewImports. cshtml* do [folderu głównego aplikacji](#arf).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="c2a5d-183">*_ViewImports. cshtml* w folderze głównym aplikacji zostanie zastosowane do wszystkich widoków w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="c2a5d-184">Skopiuj plik *_ViewImports. cshtml* do odpowiedniego folderu widoku w obszarze obszary.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="c2a5d-185">Plik *_ViewImports. cshtml* [zazwyczaj zawiera](xref:mvc/views/tag-helpers/intro) instrukcje Imports, `@using` , i `@inject` .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="c2a5d-186">Aby uzyskać więcej informacji, zobacz [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="c2a5d-187">Zmień domyślny folder obszaru, w którym są przechowywane widoki</span><span class="sxs-lookup"><span data-stu-id="c2a5d-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="c2a5d-188">Poniższy kod zmienia domyślny folder obszaru z `"Areas"` na `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="c2a5d-189">Obszary ze :::no-loc(Razor)::: stronami</span><span class="sxs-lookup"><span data-stu-id="c2a5d-189">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="c2a5d-190">Obszary ze :::no-loc(Razor)::: stronami wymagają `Areas/<area name>/Pages` folderu w folderze głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-190">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="c2a5d-191">Następująca struktura folderów jest używana z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span><span class="sxs-lookup"><span data-stu-id="c2a5d-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="c2a5d-192">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="c2a5d-192">Project name</span></span>
  * <span data-ttu-id="c2a5d-193">Obszary</span><span class="sxs-lookup"><span data-stu-id="c2a5d-193">Areas</span></span>
    * <span data-ttu-id="c2a5d-194">Produkty</span><span class="sxs-lookup"><span data-stu-id="c2a5d-194">Products</span></span>
      * <span data-ttu-id="c2a5d-195">Strony</span><span class="sxs-lookup"><span data-stu-id="c2a5d-195">Pages</span></span>
        * <span data-ttu-id="c2a5d-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="c2a5d-196">_ViewImports</span></span>
        * <span data-ttu-id="c2a5d-197">Informacje</span><span class="sxs-lookup"><span data-stu-id="c2a5d-197">About</span></span>
        * <span data-ttu-id="c2a5d-198">Indeks</span><span class="sxs-lookup"><span data-stu-id="c2a5d-198">Index</span></span>
    * <span data-ttu-id="c2a5d-199">Usługi</span><span class="sxs-lookup"><span data-stu-id="c2a5d-199">Services</span></span>
      * <span data-ttu-id="c2a5d-200">Strony</span><span class="sxs-lookup"><span data-stu-id="c2a5d-200">Pages</span></span>
        * <span data-ttu-id="c2a5d-201">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="c2a5d-201">Manage</span></span>
          * <span data-ttu-id="c2a5d-202">Informacje</span><span class="sxs-lookup"><span data-stu-id="c2a5d-202">About</span></span>
          * <span data-ttu-id="c2a5d-203">Indeks</span><span class="sxs-lookup"><span data-stu-id="c2a5d-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="c2a5d-204">Generowanie linków przy użyciu :::no-loc(Razor)::: stron i obszarów</span><span class="sxs-lookup"><span data-stu-id="c2a5d-204">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="c2a5d-205">Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje Generowanie łącza z określonym obszarem (na przykład `asp-area="Products"` ):</span><span class="sxs-lookup"><span data-stu-id="c2a5d-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="c2a5d-206">Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający poprzednie linki i te same linki bez określania obszaru.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="c2a5d-207">Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="c2a5d-208">Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy są przywoływane ze strony w tym samym obszarze.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="c2a5d-209">Gdy obszar nie zostanie określony, routing zależy od wartości *otoczenia* .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="c2a5d-210">Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="c2a5d-211">W wielu przypadkach dla przykładowej aplikacji korzystanie z wartości otoczenia powoduje wygenerowanie nieprawidłowych linków.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="c2a5d-212">Rozważmy na przykład linki wygenerowane na podstawie następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="c2a5d-213">Dla poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-213">For the preceding code:</span></span>

* <span data-ttu-id="c2a5d-214">Link wygenerowany z `<a asp-page="/Manage/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="c2a5d-215">Na przykład, `/Services/Manage/` , `/Services/Manage/Index` lub `/Services/Manage/About` .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="c2a5d-216">Link wygenerowany z `<a asp-page="/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `/Home` .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="c2a5d-217">Kod pochodzi z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="c2a5d-218">Importowanie przestrzeni nazw i pomocników tagów przy użyciu pliku _ViewImports</span><span class="sxs-lookup"><span data-stu-id="c2a5d-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="c2a5d-219">Plik *_ViewImports. cshtml* można dodać do każdego folderu *stron* obszaru, aby zaimportować przestrzeń nazw i Tagi pomocników do każdej :::no-loc(Razor)::: strony w folderze.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="c2a5d-220">Weź pod uwagę obszar *usług* przykładowego kodu, który nie zawiera pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c2a5d-221">Następujące znaczniki przedstawiają stronę */Services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-221">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="c2a5d-222">W powyższym znaczniku:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-222">In the preceding markup:</span></span>

* <span data-ttu-id="c2a5d-223">W pełni kwalifikowana nazwa domeny musi zostać użyta do określenia modelu ( `@model RPareas.Areas.Services.Pages.Manage.AboutModel` ).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="c2a5d-224">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) są włączani przez `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="c2a5d-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="c2a5d-225">W przykładowym pobieranym obszarze produkty znajdują się następujące *_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="c2a5d-226">Następujące znaczniki przedstawiają stronę */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-226">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="c2a5d-227">W poprzednim pliku przestrzeń nazw i `@addTagHelper` dyrektywa są importowane do pliku przez *obszary/produkty/strony/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="c2a5d-228">Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika tagów](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="c2a5d-229">Układ współużytkowany dla :::no-loc(Razor)::: obszarów stron</span><span class="sxs-lookup"><span data-stu-id="c2a5d-229">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="c2a5d-230">Aby udostępnić wspólny układ całej aplikacji, Przenieś *_ViewStart. cshtml* do folderu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="c2a5d-231">Publikowanie obszarów</span><span class="sxs-lookup"><span data-stu-id="c2a5d-231">Publishing Areas</span></span>

<span data-ttu-id="c2a5d-232">Wszystkie pliki \*. cshtml i pliki znajdujące się w katalogu *wwwroot* są publikowane w danych wyjściowych `<Project Sdk="Microsoft.NET.Sdk.Web">` , gdy są zawarte w pliku \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2a5d-233">Obszary są funkcją ASP.NET używaną do organizowania powiązanych funkcji w grupie jako oddzielnej przestrzeni nazw (dla routingu) i struktury folderów (dla widoków).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="c2a5d-234">Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie innego parametru trasy, `area` do `controller` i `action` lub :::no-loc(Razor)::: strony `page` .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="c2a5d-235">Obszary umożliwiają dzielenie aplikacji sieci Web na ASP.NET Core do mniejszych grup funkcjonalnych, z których każdy ma własny zestaw :::no-loc(Razor)::: stron, kontrolerów, widoków i modeli.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="c2a5d-236">Obszar jest efektywnie strukturą wewnątrz aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="c2a5d-237">W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Pages, model, Controller i View, są przechowywane w różnych folderach.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="c2a5d-238">Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa, aby utworzyć relację między tymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="c2a5d-239">W przypadku dużej aplikacji warto podzielić aplikację na oddzielne obszary wysokiego poziomu funkcji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="c2a5d-240">Na przykład aplikacja handlu elektronicznego z wieloma jednostkami biznesowymi, takimi jak wyewidencjonowywanie, rozliczenia i wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="c2a5d-241">Każda z tych jednostek ma własny obszar, który zawiera widoki, kontrolery, :::no-loc(Razor)::: strony i modele.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-241">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="c2a5d-242">Rozważ użycie obszarów w projekcie, gdy:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="c2a5d-243">Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie oddzielić.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="c2a5d-244">Chcesz podzielić aplikację na partycje, tak aby każdy obszar funkcjonalny mógł działać niezależnie.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="c2a5d-245">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="c2a5d-246">Przykład pobierania zawiera podstawową aplikację do testowania obszarów.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="c2a5d-247">Jeśli używasz :::no-loc(Razor)::: stron, zobacz [obszary ze :::no-loc(Razor)::: stronami](#areas-with-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-247">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="c2a5d-248">Obszary dla kontrolerów z widokami</span><span class="sxs-lookup"><span data-stu-id="c2a5d-248">Areas for controllers with views</span></span>

<span data-ttu-id="c2a5d-249">Typowy ASP.NET Core aplikacja internetowa korzystająca z obszarów, kontrolerów i widoków zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="c2a5d-250">[Struktura folderów obszaru](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="c2a5d-251">Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="c2a5d-252">[Trasa obszaru dodana do uruchamiania](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="c2a5d-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="c2a5d-253">Struktura folderów obszaru</span><span class="sxs-lookup"><span data-stu-id="c2a5d-253">Area folder structure</span></span>

<span data-ttu-id="c2a5d-254">Weź pod uwagę aplikację, która ma dwie grupy logiczne, *produkty* i *usługi* .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-254">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="c2a5d-255">Przy użyciu obszarów struktura folderów będzie wyglądać podobnie do następujących:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="c2a5d-256">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="c2a5d-256">Project name</span></span>
  * <span data-ttu-id="c2a5d-257">Obszary</span><span class="sxs-lookup"><span data-stu-id="c2a5d-257">Areas</span></span>
    * <span data-ttu-id="c2a5d-258">Produkty</span><span class="sxs-lookup"><span data-stu-id="c2a5d-258">Products</span></span>
      * <span data-ttu-id="c2a5d-259">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="c2a5d-259">Controllers</span></span>
        * <span data-ttu-id="c2a5d-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="c2a5d-260">HomeController.cs</span></span>
        * <span data-ttu-id="c2a5d-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="c2a5d-261">ManageController.cs</span></span>
      * <span data-ttu-id="c2a5d-262">Widoki</span><span class="sxs-lookup"><span data-stu-id="c2a5d-262">Views</span></span>
        * <span data-ttu-id="c2a5d-263">Strona główna</span><span class="sxs-lookup"><span data-stu-id="c2a5d-263">Home</span></span>
          * <span data-ttu-id="c2a5d-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-264">Index.cshtml</span></span>
        * <span data-ttu-id="c2a5d-265">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="c2a5d-265">Manage</span></span>
          * <span data-ttu-id="c2a5d-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-266">Index.cshtml</span></span>
          * <span data-ttu-id="c2a5d-267">About. cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-267">About.cshtml</span></span>
    * <span data-ttu-id="c2a5d-268">Usługi</span><span class="sxs-lookup"><span data-stu-id="c2a5d-268">Services</span></span>
      * <span data-ttu-id="c2a5d-269">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="c2a5d-269">Controllers</span></span>
        * <span data-ttu-id="c2a5d-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="c2a5d-270">HomeController.cs</span></span>
      * <span data-ttu-id="c2a5d-271">Widoki</span><span class="sxs-lookup"><span data-stu-id="c2a5d-271">Views</span></span>
        * <span data-ttu-id="c2a5d-272">Strona główna</span><span class="sxs-lookup"><span data-stu-id="c2a5d-272">Home</span></span>
          * <span data-ttu-id="c2a5d-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-273">Index.cshtml</span></span>

<span data-ttu-id="c2a5d-274">Chociaż poprzedni układ jest typowy w przypadku korzystania z obszarów, do korzystania z tej struktury folderów są wymagane tylko pliki widoku.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="c2a5d-275">Wyświetl wyszukiwania odnajdywania dla zgodnego pliku widoku obszaru w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="c2a5d-276">Skojarz kontroler z obszarem</span><span class="sxs-lookup"><span data-stu-id="c2a5d-276">Associate the controller with an Area</span></span>

<span data-ttu-id="c2a5d-277">Kontrolery obszaru są oznaczone atrybutem [ &lbrack; obszaru &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="c2a5d-278">Dodaj trasę obszaru</span><span class="sxs-lookup"><span data-stu-id="c2a5d-278">Add Area route</span></span>

<span data-ttu-id="c2a5d-279">Trasy obszaru zwykle używają konwencjonalnego routingu, a nie routingu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="c2a5d-280">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="c2a5d-281">Ogólnie rzecz biorąc, trasy z obszarami należy umieścić wcześniej w tabeli tras, ponieważ są one bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="c2a5d-282">`{area:...}` może służyć jako token w szablonach tras, jeśli przestrzeń adresów URL jest jednolita dla wszystkich obszarów:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="c2a5d-283">W poprzednim kodzie, `exists` stosuje ograniczenie, które musi być zgodne z obszarem.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="c2a5d-284">Korzystanie z programu `{area:...}` to najmniej skomplikowany mechanizm dodawania routingu do obszarów.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="c2a5d-285">Poniższy kod używa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> do tworzenia dwóch nazwanych tras obszaru:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="c2a5d-286">W przypadku korzystania `MapAreaRoute` z programu z ASP.NET Core 2,2, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/7772)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="c2a5d-287">Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="c2a5d-288">Generowanie linków z obszarami MVC</span><span class="sxs-lookup"><span data-stu-id="c2a5d-288">Link generation with MVC areas</span></span>

<span data-ttu-id="c2a5d-289">Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) pokazuje generowanie linków z określonym obszarem:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="c2a5d-290">Linki generowane za pomocą powyższego kodu są prawidłowe w dowolnym miejscu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="c2a5d-291">Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający poprzednie linki i te same linki bez określania obszaru.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="c2a5d-292">Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="c2a5d-293">Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy jest przywoływany ze strony w tym samym obszarze i kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="c2a5d-294">Gdy nie określono obszaru lub kontrolera, routing zależy od wartości *otoczenia* .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="c2a5d-295">Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="c2a5d-296">W wielu przypadkach dla przykładowej aplikacji korzystanie z wartości otoczenia powoduje wygenerowanie nieprawidłowych linków.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="c2a5d-297">Aby uzyskać więcej informacji, zobacz [routing do kontrolera akcji](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="c2a5d-298">Układ współużytkowany dla obszarów korzystających z pliku _ViewStart. cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="c2a5d-299">Aby udostępnić wspólny układ całej aplikacji, Przenieś *_ViewStart. cshtml* do folderu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="c2a5d-300">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="c2a5d-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="c2a5d-301">W swojej lokalizacji standardowej */Views/_ViewImports. cshtml* nie ma zastosowania do obszarów.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="c2a5d-302">Aby użyć wspólnych [pomocników tagów](xref:mvc/views/tag-helpers/intro), `@using` lub `@inject` w Twoim regionie, upewnij się, że odpowiedni plik *_ViewImports. cshtml* [ma zastosowanie do widoków obszaru](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="c2a5d-303">Jeśli chcesz mieć takie samo zachowanie we wszystkich widokach, Przenieś */Views/_ViewImports. cshtml* do katalogu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="c2a5d-304">Zmień domyślny folder obszaru, w którym są przechowywane widoki</span><span class="sxs-lookup"><span data-stu-id="c2a5d-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="c2a5d-305">Poniższy kod zmienia domyślny folder obszaru z `"Areas"` na `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="c2a5d-306">Obszary ze :::no-loc(Razor)::: stronami</span><span class="sxs-lookup"><span data-stu-id="c2a5d-306">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="c2a5d-307">Obszary ze :::no-loc(Razor)::: stronami wymagają `Areas/<area name>/Pages` folderu w folderze głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-307">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="c2a5d-308">Następująca struktura folderów jest używana z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span><span class="sxs-lookup"><span data-stu-id="c2a5d-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="c2a5d-309">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="c2a5d-309">Project name</span></span>
  * <span data-ttu-id="c2a5d-310">Obszary</span><span class="sxs-lookup"><span data-stu-id="c2a5d-310">Areas</span></span>
    * <span data-ttu-id="c2a5d-311">Produkty</span><span class="sxs-lookup"><span data-stu-id="c2a5d-311">Products</span></span>
      * <span data-ttu-id="c2a5d-312">Strony</span><span class="sxs-lookup"><span data-stu-id="c2a5d-312">Pages</span></span>
        * <span data-ttu-id="c2a5d-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="c2a5d-313">_ViewImports</span></span>
        * <span data-ttu-id="c2a5d-314">Informacje</span><span class="sxs-lookup"><span data-stu-id="c2a5d-314">About</span></span>
        * <span data-ttu-id="c2a5d-315">Indeks</span><span class="sxs-lookup"><span data-stu-id="c2a5d-315">Index</span></span>
    * <span data-ttu-id="c2a5d-316">Usługi</span><span class="sxs-lookup"><span data-stu-id="c2a5d-316">Services</span></span>
      * <span data-ttu-id="c2a5d-317">Strony</span><span class="sxs-lookup"><span data-stu-id="c2a5d-317">Pages</span></span>
        * <span data-ttu-id="c2a5d-318">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="c2a5d-318">Manage</span></span>
          * <span data-ttu-id="c2a5d-319">Informacje</span><span class="sxs-lookup"><span data-stu-id="c2a5d-319">About</span></span>
          * <span data-ttu-id="c2a5d-320">Indeks</span><span class="sxs-lookup"><span data-stu-id="c2a5d-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="c2a5d-321">Generowanie linków przy użyciu :::no-loc(Razor)::: stron i obszarów</span><span class="sxs-lookup"><span data-stu-id="c2a5d-321">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="c2a5d-322">Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje Generowanie łącza z określonym obszarem (na przykład `asp-area="Products"` ):</span><span class="sxs-lookup"><span data-stu-id="c2a5d-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="c2a5d-323">Linki generowane za pomocą powyższego kodu są prawidłowe w dowolnym miejscu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="c2a5d-324">Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający poprzednie linki i te same linki bez określania obszaru.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="c2a5d-325">Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="c2a5d-326">Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy są przywoływane ze strony w tym samym obszarze.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="c2a5d-327">Gdy obszar nie zostanie określony, routing zależy od wartości *otoczenia* .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="c2a5d-328">Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="c2a5d-329">W wielu przypadkach dla przykładowej aplikacji korzystanie z wartości otoczenia powoduje wygenerowanie nieprawidłowych linków.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="c2a5d-330">Rozważmy na przykład linki wygenerowane na podstawie następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="c2a5d-331">Dla poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-331">For the preceding code:</span></span>

* <span data-ttu-id="c2a5d-332">Link wygenerowany z `<a asp-page="/Manage/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="c2a5d-333">Na przykład, `/Services/Manage/` , `/Services/Manage/Index` lub `/Services/Manage/About` .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="c2a5d-334">Link wygenerowany z `<a asp-page="/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `/Home` .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="c2a5d-335">Kod pochodzi z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="c2a5d-336">Importowanie przestrzeni nazw i pomocników tagów przy użyciu pliku _ViewImports</span><span class="sxs-lookup"><span data-stu-id="c2a5d-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="c2a5d-337">Plik *_ViewImports. cshtml* można dodać do każdego folderu *stron* obszaru, aby zaimportować przestrzeń nazw i Tagi pomocników do każdej :::no-loc(Razor)::: strony w folderze.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="c2a5d-338">Weź pod uwagę obszar *usług* przykładowego kodu, który nie zawiera pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c2a5d-339">Następujące znaczniki przedstawiają stronę */Services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-339">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="c2a5d-340">W powyższym znaczniku:</span><span class="sxs-lookup"><span data-stu-id="c2a5d-340">In the preceding markup:</span></span>

* <span data-ttu-id="c2a5d-341">W pełni kwalifikowana nazwa domeny musi zostać użyta do określenia modelu ( `@model RPareas.Areas.Services.Pages.Manage.AboutModel` ).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="c2a5d-342">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) są włączani przez `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="c2a5d-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="c2a5d-343">W przykładowym pobieranym obszarze produkty znajdują się następujące *_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="c2a5d-344">Następujące znaczniki przedstawiają stronę */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="c2a5d-344">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="c2a5d-345">W poprzednim pliku przestrzeń nazw i `@addTagHelper` dyrektywa są importowane do pliku przez *obszary/produkty/strony/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c2a5d-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="c2a5d-346">Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika tagów](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="c2a5d-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="c2a5d-347">Układ współużytkowany dla :::no-loc(Razor)::: obszarów stron</span><span class="sxs-lookup"><span data-stu-id="c2a5d-347">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="c2a5d-348">Aby udostępnić wspólny układ całej aplikacji, Przenieś *_ViewStart. cshtml* do folderu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="c2a5d-349">Publikowanie obszarów</span><span class="sxs-lookup"><span data-stu-id="c2a5d-349">Publishing Areas</span></span>

<span data-ttu-id="c2a5d-350">Wszystkie pliki \*. cshtml i pliki znajdujące się w katalogu *wwwroot* są publikowane w danych wyjściowych `<Project Sdk="Microsoft.NET.Sdk.Web">` , gdy są zawarte w pliku \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="c2a5d-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
