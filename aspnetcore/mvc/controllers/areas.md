---
title: Obszary w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak obszary są ASP.NET funkcją MVC używaną do organizowania powiązanych funkcji w grupę jako oddzielny obszar nazw (do routingu) i strukturę folderów (dla widoków).
ms.author: riande
ms.date: 03/21/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 8859bc52416ff657036198c73f63b8b0a0201e11
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625932"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="2c566-103">Obszary w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c566-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="2c566-104">Przez [Dhananjay Kumar](https://twitter.com/debug_mode) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2c566-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2c566-105">Obszary są ASP.NET funkcją służącą do organizowania powiązanych funkcji w grupie jako osobnej:</span><span class="sxs-lookup"><span data-stu-id="2c566-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="2c566-106">Obszar nazw dla routingu.</span><span class="sxs-lookup"><span data-stu-id="2c566-106">Namespace for routing.</span></span>
* <span data-ttu-id="2c566-107">Struktura folderów dla widoków i stron Razor.</span><span class="sxs-lookup"><span data-stu-id="2c566-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="2c566-108">Za pomocą obszarów tworzy hierarchię do celów routingu, `action` dodając inny parametr `page`trasy, `area`, do `controller` lub razor Strony .</span><span class="sxs-lookup"><span data-stu-id="2c566-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="2c566-109">Obszary umożliwiają podział ASP.NET podstawowej aplikacji sieci Web na mniejsze grupy funkcjonalne, z których każda ma własny zestaw stron Razor, kontrolerów, widoków i modeli.</span><span class="sxs-lookup"><span data-stu-id="2c566-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="2c566-110">Obszar jest skutecznie strukturą wewnątrz aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="2c566-111">W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Strony, Model, Kontroler i Widok, są przechowywane w różnych folderach.</span><span class="sxs-lookup"><span data-stu-id="2c566-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="2c566-112">Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa do tworzenia relacji między tymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="2c566-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="2c566-113">W przypadku dużej aplikacji może być korzystne partycjonowanie aplikacji na oddzielne obszary wysokiego poziomu funkcjonalności.</span><span class="sxs-lookup"><span data-stu-id="2c566-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="2c566-114">Na przykład aplikacja e-commerce z wieloma jednostkami biznesowymi, takimi jak kasa, rozliczenia i wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="2c566-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="2c566-115">Każda z tych jednostek ma swój własny obszar zawierający widoki, kontrolery, strony razor i modele.</span><span class="sxs-lookup"><span data-stu-id="2c566-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="2c566-116">Rozważ użycie obszarów w projekcie, gdy:</span><span class="sxs-lookup"><span data-stu-id="2c566-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="2c566-117">Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie rozdzielić.</span><span class="sxs-lookup"><span data-stu-id="2c566-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="2c566-118">Chcesz podzielić aplikację, aby każdy obszar funkcjonalny mógł pracować niezależnie.</span><span class="sxs-lookup"><span data-stu-id="2c566-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="2c566-119">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) [(jak pobrać).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2c566-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2c566-120">Przykład pobierania zawiera podstawową aplikację do testowania obszarów.</span><span class="sxs-lookup"><span data-stu-id="2c566-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="2c566-121">Jeśli używasz stron Razor, zobacz [Obszary ze stronami Razor](#areas-with-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="2c566-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="2c566-122">Obszary dla kontrolerów z widokami</span><span class="sxs-lookup"><span data-stu-id="2c566-122">Areas for controllers with views</span></span>

<span data-ttu-id="2c566-123">Typowa aplikacja sieci Web ASP.NET Core przy użyciu obszarów, kontrolerów i widoków zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="2c566-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="2c566-124">[Struktura folderów Obszar](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="2c566-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="2c566-125">Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:</span><span class="sxs-lookup"><span data-stu-id="2c566-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="2c566-126">[Trasa obszaru dodana do uruchomienia:](#add-area-route)</span><span class="sxs-lookup"><span data-stu-id="2c566-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="2c566-127">Struktura folderów obszarowych</span><span class="sxs-lookup"><span data-stu-id="2c566-127">Area folder structure</span></span>

<span data-ttu-id="2c566-128">Należy wziąć pod uwagę aplikację, która ma dwie grupy logiczne, *Produkty* i *usługi*.</span><span class="sxs-lookup"><span data-stu-id="2c566-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="2c566-129">Przy użyciu obszarów struktura folderów będzie podobna do następującej:</span><span class="sxs-lookup"><span data-stu-id="2c566-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="2c566-130">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="2c566-130">Project name</span></span>
  * <span data-ttu-id="2c566-131">Obszary</span><span class="sxs-lookup"><span data-stu-id="2c566-131">Areas</span></span>
    * <span data-ttu-id="2c566-132">Produkty</span><span class="sxs-lookup"><span data-stu-id="2c566-132">Products</span></span>
      * <span data-ttu-id="2c566-133">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="2c566-133">Controllers</span></span>
        * <span data-ttu-id="2c566-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2c566-134">HomeController.cs</span></span>
        * <span data-ttu-id="2c566-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="2c566-135">ManageController.cs</span></span>
      * <span data-ttu-id="2c566-136">Widoki</span><span class="sxs-lookup"><span data-stu-id="2c566-136">Views</span></span>
        * <span data-ttu-id="2c566-137">Strona główna</span><span class="sxs-lookup"><span data-stu-id="2c566-137">Home</span></span>
          * <span data-ttu-id="2c566-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-138">Index.cshtml</span></span>
        * <span data-ttu-id="2c566-139">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="2c566-139">Manage</span></span>
          * <span data-ttu-id="2c566-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-140">Index.cshtml</span></span>
          * <span data-ttu-id="2c566-141">Informacje.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-141">About.cshtml</span></span>
    * <span data-ttu-id="2c566-142">Usługi</span><span class="sxs-lookup"><span data-stu-id="2c566-142">Services</span></span>
      * <span data-ttu-id="2c566-143">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="2c566-143">Controllers</span></span>
        * <span data-ttu-id="2c566-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2c566-144">HomeController.cs</span></span>
      * <span data-ttu-id="2c566-145">Widoki</span><span class="sxs-lookup"><span data-stu-id="2c566-145">Views</span></span>
        * <span data-ttu-id="2c566-146">Strona główna</span><span class="sxs-lookup"><span data-stu-id="2c566-146">Home</span></span>
          * <span data-ttu-id="2c566-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-147">Index.cshtml</span></span>

<span data-ttu-id="2c566-148">Podczas gdy poprzedni układ jest typowy podczas korzystania z obszarów, tylko pliki widoku są wymagane do korzystania z tej struktury folderów.</span><span class="sxs-lookup"><span data-stu-id="2c566-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="2c566-149">Wyświetl wyszukiwanie odnajdowania pasującego pliku widoku obszaru w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="2c566-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="2c566-150">Skojarzenie kontrolera z obszarem</span><span class="sxs-lookup"><span data-stu-id="2c566-150">Associate the controller with an Area</span></span>

<span data-ttu-id="2c566-151">Kontrolery obszarów są oznaczone atrybutem [ &lbrack;Obszar:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)</span><span class="sxs-lookup"><span data-stu-id="2c566-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="2c566-152">Dodaj trasę obszaru</span><span class="sxs-lookup"><span data-stu-id="2c566-152">Add Area route</span></span>

<span data-ttu-id="2c566-153">Trasy obszarowe zazwyczaj używają [routingu konwencjonalnego,](xref:mvc/controllers/routing#cr) a nie [routingu atrybutów](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="2c566-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="2c566-154">Routing konwencjonalny jest zależny od zamówienia.</span><span class="sxs-lookup"><span data-stu-id="2c566-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2c566-155">Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej w tabeli tras, ponieważ są bardziej szczegółowe niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="2c566-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2c566-156">`{area:...}`może służyć jako token w szablonach trasy, jeśli przestrzeń adresów URL jest jednolita we wszystkich obszarach:</span><span class="sxs-lookup"><span data-stu-id="2c566-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="2c566-157">W poprzednim kodzie `exists` stosuje ograniczenie, które trasa musi pasować do obszaru.</span><span class="sxs-lookup"><span data-stu-id="2c566-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="2c566-158">Korzystanie `{area:...}` `MapControllerRoute`z:</span><span class="sxs-lookup"><span data-stu-id="2c566-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="2c566-159">Jest najmniej skomplikowanym mechanizmem dodawania routingu do obszarów.</span><span class="sxs-lookup"><span data-stu-id="2c566-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="2c566-160">Dopasowuje wszystkie `[Area("Area name")]` kontrolery z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="2c566-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="2c566-161">Poniższy kod <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> służy do tworzenia dwóch nazwanych tras obszaru:</span><span class="sxs-lookup"><span data-stu-id="2c566-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="2c566-162">Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="2c566-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="2c566-163">Generowanie łączy z obszarami MVC</span><span class="sxs-lookup"><span data-stu-id="2c566-163">Link generation with MVC areas</span></span>

<span data-ttu-id="2c566-164">Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) pokazuje generowanie łączy z określonym obszarem:</span><span class="sxs-lookup"><span data-stu-id="2c566-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2c566-165">Przykładowe pobieranie zawiera [widok częściowy,](xref:mvc/views/partial) który zawiera:</span><span class="sxs-lookup"><span data-stu-id="2c566-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="2c566-166">Powyższe łącza.</span><span class="sxs-lookup"><span data-stu-id="2c566-166">The preceding links.</span></span>
* <span data-ttu-id="2c566-167">Łącza podobne do poprzedniego `area` z wyjątkiem nie jest określony.</span><span class="sxs-lookup"><span data-stu-id="2c566-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="2c566-168">Widok częściowy odwołuje się do [pliku układu,](xref:mvc/views/layout)więc każda strona w aplikacji wyświetla wygenerowane łącza.</span><span class="sxs-lookup"><span data-stu-id="2c566-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2c566-169">Łącza generowane bez określania obszaru są prawidłowe tylko wtedy, gdy odwołuje się do strony w tym samym obszarze i kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2c566-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="2c566-170">Jeśli obszar lub kontroler nie jest określony, routing zależy od wartości [otoczenia.](xref:mvc/controllers/routing#ambient)</span><span class="sxs-lookup"><span data-stu-id="2c566-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="2c566-171">Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy.</span><span class="sxs-lookup"><span data-stu-id="2c566-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2c566-172">W wielu przypadkach dla przykładowej aplikacji przy użyciu wartości otoczenia generuje niepoprawne łącza z znaczników, które nie określają obszaru.</span><span class="sxs-lookup"><span data-stu-id="2c566-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="2c566-173">Aby uzyskać więcej informacji, zobacz [Routing do akcji kontrolera](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="2c566-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="2c566-174">Układ udostępniony dla obszarów przy użyciu pliku _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="2c566-175">Aby udostępnić wspólny układ dla całej aplikacji, zachowaj *_ViewStart.cshtml* w [folderze głównym aplikacji](#arf).</span><span class="sxs-lookup"><span data-stu-id="2c566-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="2c566-176">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2c566-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="2c566-177">Folder główny aplikacji</span><span class="sxs-lookup"><span data-stu-id="2c566-177">Application root folder</span></span>

<span data-ttu-id="2c566-178">Folder główny aplikacji to folder zawierający *Startup.cs* w aplikacji internetowej utworzonej za pomocą szablonów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2c566-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="2c566-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="2c566-180">*/Views/_ViewImports.cshtml*, dla MVC i */Pages/_ViewImports.cshtml* dla stron Razor, nie jest importowany do widoków w obszarach.</span><span class="sxs-lookup"><span data-stu-id="2c566-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="2c566-181">Użyj jednego z następujących podejść, aby zapewnić import widoku do wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="2c566-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="2c566-182">Dodaj *_ViewImports.cshtml* do [folderu głównego aplikacji](#arf).</span><span class="sxs-lookup"><span data-stu-id="2c566-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="2c566-183">*_ViewImports.cshtml* w folderze głównym aplikacji będzie stosowany do wszystkich widoków w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="2c566-184">Skopiuj plik *_ViewImports.cshtml* do odpowiedniego folderu widoku w obszarze obszary.</span><span class="sxs-lookup"><span data-stu-id="2c566-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="2c566-185">Plik *_ViewImports.cshtml* zazwyczaj zawiera [importowanie](xref:mvc/views/tag-helpers/intro) pomocników znaczników `@inject` i instrukcje. `@using`</span><span class="sxs-lookup"><span data-stu-id="2c566-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="2c566-186">Aby uzyskać więcej informacji, zobacz [Importowanie dyrektyw udostępnionych](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2c566-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="2c566-187">Zmienianie domyślnego folderu obszaru, w którym są przechowywane widoki</span><span class="sxs-lookup"><span data-stu-id="2c566-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="2c566-188">Poniższy kod zmienia domyślny `"Areas"` `"MyAreas"`folder obszarowy z:</span><span class="sxs-lookup"><span data-stu-id="2c566-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="2c566-189">Obszary ze stronami razor</span><span class="sxs-lookup"><span data-stu-id="2c566-189">Areas with Razor Pages</span></span>

<span data-ttu-id="2c566-190">Obszary ze stronami `Areas/<area name>/Pages` Razor wymagają folderu w katalogu głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="2c566-191">Z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)używana jest następująca struktura folderów:</span><span class="sxs-lookup"><span data-stu-id="2c566-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="2c566-192">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="2c566-192">Project name</span></span>
  * <span data-ttu-id="2c566-193">Obszary</span><span class="sxs-lookup"><span data-stu-id="2c566-193">Areas</span></span>
    * <span data-ttu-id="2c566-194">Produkty</span><span class="sxs-lookup"><span data-stu-id="2c566-194">Products</span></span>
      * <span data-ttu-id="2c566-195">Strony</span><span class="sxs-lookup"><span data-stu-id="2c566-195">Pages</span></span>
        * <span data-ttu-id="2c566-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="2c566-196">_ViewImports</span></span>
        * <span data-ttu-id="2c566-197">Informacje</span><span class="sxs-lookup"><span data-stu-id="2c566-197">About</span></span>
        * <span data-ttu-id="2c566-198">Indeks</span><span class="sxs-lookup"><span data-stu-id="2c566-198">Index</span></span>
    * <span data-ttu-id="2c566-199">Usługi</span><span class="sxs-lookup"><span data-stu-id="2c566-199">Services</span></span>
      * <span data-ttu-id="2c566-200">Strony</span><span class="sxs-lookup"><span data-stu-id="2c566-200">Pages</span></span>
        * <span data-ttu-id="2c566-201">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="2c566-201">Manage</span></span>
          * <span data-ttu-id="2c566-202">Informacje</span><span class="sxs-lookup"><span data-stu-id="2c566-202">About</span></span>
          * <span data-ttu-id="2c566-203">Indeks</span><span class="sxs-lookup"><span data-stu-id="2c566-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="2c566-204">Generowanie łączyć się ze stronami i obszarami razor</span><span class="sxs-lookup"><span data-stu-id="2c566-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="2c566-205">Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje generowanie łączy `asp-area="Products"`z określonym obszarem (na przykład):</span><span class="sxs-lookup"><span data-stu-id="2c566-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2c566-206">Przykładowe pobieranie zawiera [widok częściowy,](xref:mvc/views/partial) który zawiera poprzednie łącza i te same łącza bez określania obszaru.</span><span class="sxs-lookup"><span data-stu-id="2c566-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="2c566-207">Widok częściowy odwołuje się do [pliku układu,](xref:mvc/views/layout)więc każda strona w aplikacji wyświetla wygenerowane łącza.</span><span class="sxs-lookup"><span data-stu-id="2c566-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2c566-208">Łącza wygenerowane bez określania obszaru są prawidłowe tylko wtedy, gdy odwołuje się do strony w tym samym obszarze.</span><span class="sxs-lookup"><span data-stu-id="2c566-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="2c566-209">Jeśli obszar nie jest określony, routing zależy od wartości *otoczenia.*</span><span class="sxs-lookup"><span data-stu-id="2c566-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="2c566-210">Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy.</span><span class="sxs-lookup"><span data-stu-id="2c566-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2c566-211">W wielu przypadkach dla przykładowej aplikacji przy użyciu wartości otoczenia generuje niepoprawne łącza.</span><span class="sxs-lookup"><span data-stu-id="2c566-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="2c566-212">Rozważmy na przykład łącza wygenerowane na podstawie następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2c566-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="2c566-213">Dla poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="2c566-213">For the preceding code:</span></span>

* <span data-ttu-id="2c566-214">Łącze `<a asp-page="/Manage/About">` wygenerowane z jest poprawne tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze.</span><span class="sxs-lookup"><span data-stu-id="2c566-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="2c566-215">Na przykład `/Services/Manage/` `/Services/Manage/Index`, `/Services/Manage/About`, lub .</span><span class="sxs-lookup"><span data-stu-id="2c566-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="2c566-216">Łącze `<a asp-page="/About">` wygenerowane z jest poprawne tylko wtedy, gdy ostatnie żądanie dotyczyło strony w pliku `/Home`.</span><span class="sxs-lookup"><span data-stu-id="2c566-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="2c566-217">Kod pochodzi z [przykładowego pobrania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="2c566-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="2c566-218">Importowanie obszaru nazw i pomocników znaczników za pomocą pliku _ViewImports</span><span class="sxs-lookup"><span data-stu-id="2c566-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="2c566-219">Plik *_ViewImports.cshtml* można dodać do każdego folderu *Strony* obszaru, aby zaimportować obszar nazw i Pomocników znaczników do każdej strony Razor w folderze.</span><span class="sxs-lookup"><span data-stu-id="2c566-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="2c566-220">Należy wziąć pod uwagę *usługi* obszaru przykładowego kodu, który nie zawiera *pliku _ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="2c566-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2c566-221">Następujące znaczniki pokazuje */Services/Manage/About* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="2c566-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="2c566-222">W poprzednim znaczniku:</span><span class="sxs-lookup"><span data-stu-id="2c566-222">In the preceding markup:</span></span>

* <span data-ttu-id="2c566-223">Do określenia modelu musi służyć w pełni`@model RPareas.Areas.Services.Pages.Manage.AboutModel`kwalifikowana nazwa domeny ( ).</span><span class="sxs-lookup"><span data-stu-id="2c566-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="2c566-224">[Pomocnik tagów](xref:mvc/views/tag-helpers/intro) jest włączona przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="2c566-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="2c566-225">W przykładowym pliku pobierania obszar Produkty zawiera następujący *plik _ViewImports.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="2c566-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="2c566-226">Następujące znaczniki pokazuje */Produkty/ O* Razor Strony:</span><span class="sxs-lookup"><span data-stu-id="2c566-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="2c566-227">W poprzednim pliku obszar nazw `@addTagHelper` i dyrektywa są importowane do pliku przez plik *Obszary/Produkty/Strony/_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="2c566-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="2c566-228">Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika znaczników](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [importowanie dyrektyw udostępnionych](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2c566-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="2c566-229">Układ udostępniony dla obszarów stron razor</span><span class="sxs-lookup"><span data-stu-id="2c566-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="2c566-230">Aby udostępnić wspólny układ dla całej aplikacji, przenieś *_ViewStart.cshtml* do folderu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="2c566-231">Obszary wydawnicze</span><span class="sxs-lookup"><span data-stu-id="2c566-231">Publishing Areas</span></span>

<span data-ttu-id="2c566-232">Wszystkie pliki i pliki \*.cshtml w katalogu *wwwroot* `<Project Sdk="Microsoft.NET.Sdk.Web">` są publikowane do danych wyjściowych, gdy jest zawarte w pliku \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="2c566-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2c566-233">Obszary są funkcją ASP.NET używaną do organizowania powiązanych funkcji w grupę jako oddzielny obszar nazw (do routingu) i strukturę folderów (dla widoków).</span><span class="sxs-lookup"><span data-stu-id="2c566-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="2c566-234">Za pomocą obszarów tworzy hierarchię do celów routingu, `action` dodając inny parametr `page`trasy, `area`, do `controller` lub razor Strony .</span><span class="sxs-lookup"><span data-stu-id="2c566-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="2c566-235">Obszary umożliwiają podział ASP.NET podstawowej aplikacji sieci Web na mniejsze grupy funkcjonalne, z których każda ma własny zestaw stron Razor, kontrolerów, widoków i modeli.</span><span class="sxs-lookup"><span data-stu-id="2c566-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="2c566-236">Obszar jest skutecznie strukturą wewnątrz aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="2c566-237">W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Strony, Model, Kontroler i Widok, są przechowywane w różnych folderach.</span><span class="sxs-lookup"><span data-stu-id="2c566-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="2c566-238">Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa do tworzenia relacji między tymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="2c566-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="2c566-239">W przypadku dużej aplikacji może być korzystne partycjonowanie aplikacji na oddzielne obszary wysokiego poziomu funkcjonalności.</span><span class="sxs-lookup"><span data-stu-id="2c566-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="2c566-240">Na przykład aplikacja e-commerce z wieloma jednostkami biznesowymi, takimi jak kasa, rozliczenia i wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="2c566-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="2c566-241">Każda z tych jednostek ma swój własny obszar zawierający widoki, kontrolery, strony razor i modele.</span><span class="sxs-lookup"><span data-stu-id="2c566-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="2c566-242">Rozważ użycie obszarów w projekcie, gdy:</span><span class="sxs-lookup"><span data-stu-id="2c566-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="2c566-243">Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie rozdzielić.</span><span class="sxs-lookup"><span data-stu-id="2c566-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="2c566-244">Chcesz podzielić aplikację, aby każdy obszar funkcjonalny mógł pracować niezależnie.</span><span class="sxs-lookup"><span data-stu-id="2c566-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="2c566-245">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) [(jak pobrać).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2c566-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2c566-246">Przykład pobierania zawiera podstawową aplikację do testowania obszarów.</span><span class="sxs-lookup"><span data-stu-id="2c566-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="2c566-247">Jeśli używasz stron Razor, zobacz [Obszary ze stronami Razor](#areas-with-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="2c566-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="2c566-248">Obszary dla kontrolerów z widokami</span><span class="sxs-lookup"><span data-stu-id="2c566-248">Areas for controllers with views</span></span>

<span data-ttu-id="2c566-249">Typowa aplikacja sieci Web ASP.NET Core przy użyciu obszarów, kontrolerów i widoków zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="2c566-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="2c566-250">[Struktura folderów Obszar](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="2c566-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="2c566-251">Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:</span><span class="sxs-lookup"><span data-stu-id="2c566-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="2c566-252">[Trasa obszaru dodana do uruchomienia:](#add-area-route)</span><span class="sxs-lookup"><span data-stu-id="2c566-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="2c566-253">Struktura folderów obszarowych</span><span class="sxs-lookup"><span data-stu-id="2c566-253">Area folder structure</span></span>

<span data-ttu-id="2c566-254">Należy wziąć pod uwagę aplikację, która ma dwie grupy logiczne, *Produkty* i *usługi*.</span><span class="sxs-lookup"><span data-stu-id="2c566-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="2c566-255">Przy użyciu obszarów struktura folderów będzie podobna do następującej:</span><span class="sxs-lookup"><span data-stu-id="2c566-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="2c566-256">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="2c566-256">Project name</span></span>
  * <span data-ttu-id="2c566-257">Obszary</span><span class="sxs-lookup"><span data-stu-id="2c566-257">Areas</span></span>
    * <span data-ttu-id="2c566-258">Produkty</span><span class="sxs-lookup"><span data-stu-id="2c566-258">Products</span></span>
      * <span data-ttu-id="2c566-259">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="2c566-259">Controllers</span></span>
        * <span data-ttu-id="2c566-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2c566-260">HomeController.cs</span></span>
        * <span data-ttu-id="2c566-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="2c566-261">ManageController.cs</span></span>
      * <span data-ttu-id="2c566-262">Widoki</span><span class="sxs-lookup"><span data-stu-id="2c566-262">Views</span></span>
        * <span data-ttu-id="2c566-263">Strona główna</span><span class="sxs-lookup"><span data-stu-id="2c566-263">Home</span></span>
          * <span data-ttu-id="2c566-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-264">Index.cshtml</span></span>
        * <span data-ttu-id="2c566-265">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="2c566-265">Manage</span></span>
          * <span data-ttu-id="2c566-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-266">Index.cshtml</span></span>
          * <span data-ttu-id="2c566-267">Informacje.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-267">About.cshtml</span></span>
    * <span data-ttu-id="2c566-268">Usługi</span><span class="sxs-lookup"><span data-stu-id="2c566-268">Services</span></span>
      * <span data-ttu-id="2c566-269">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="2c566-269">Controllers</span></span>
        * <span data-ttu-id="2c566-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2c566-270">HomeController.cs</span></span>
      * <span data-ttu-id="2c566-271">Widoki</span><span class="sxs-lookup"><span data-stu-id="2c566-271">Views</span></span>
        * <span data-ttu-id="2c566-272">Strona główna</span><span class="sxs-lookup"><span data-stu-id="2c566-272">Home</span></span>
          * <span data-ttu-id="2c566-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-273">Index.cshtml</span></span>

<span data-ttu-id="2c566-274">Podczas gdy poprzedni układ jest typowy podczas korzystania z obszarów, tylko pliki widoku są wymagane do korzystania z tej struktury folderów.</span><span class="sxs-lookup"><span data-stu-id="2c566-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="2c566-275">Wyświetl wyszukiwanie odnajdowania pasującego pliku widoku obszaru w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="2c566-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="2c566-276">Skojarzenie kontrolera z obszarem</span><span class="sxs-lookup"><span data-stu-id="2c566-276">Associate the controller with an Area</span></span>

<span data-ttu-id="2c566-277">Kontrolery obszarów są oznaczone atrybutem [ &lbrack;Obszar:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)</span><span class="sxs-lookup"><span data-stu-id="2c566-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="2c566-278">Dodaj trasę obszaru</span><span class="sxs-lookup"><span data-stu-id="2c566-278">Add Area route</span></span>

<span data-ttu-id="2c566-279">Trasy obszarowe zazwyczaj używają routingu konwencjonalnego, a nie routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2c566-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="2c566-280">Routing konwencjonalny jest zależny od zamówienia.</span><span class="sxs-lookup"><span data-stu-id="2c566-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2c566-281">Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej w tabeli tras, ponieważ są bardziej szczegółowe niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="2c566-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2c566-282">`{area:...}`może służyć jako token w szablonach trasy, jeśli przestrzeń adresów URL jest jednolita we wszystkich obszarach:</span><span class="sxs-lookup"><span data-stu-id="2c566-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="2c566-283">W poprzednim kodzie `exists` stosuje ograniczenie, które trasa musi pasować do obszaru.</span><span class="sxs-lookup"><span data-stu-id="2c566-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="2c566-284">Używanie `{area:...}` jest najmniej skomplikowanym mechanizmem do dodawania routingu do obszarów.</span><span class="sxs-lookup"><span data-stu-id="2c566-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="2c566-285">Poniższy kod <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> służy do tworzenia dwóch nazwanych tras obszaru:</span><span class="sxs-lookup"><span data-stu-id="2c566-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="2c566-286">Podczas `MapAreaRoute` korzystania z ASP.NET Core 2.2, zobacz [ten problem GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="2c566-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="2c566-287">Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="2c566-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="2c566-288">Generowanie łączy z obszarami MVC</span><span class="sxs-lookup"><span data-stu-id="2c566-288">Link generation with MVC areas</span></span>

<span data-ttu-id="2c566-289">Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) pokazuje generowanie łączy z określonym obszarem:</span><span class="sxs-lookup"><span data-stu-id="2c566-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2c566-290">Łącza wygenerowane za pomocą poprzedniego kodu są prawidłowe w dowolnym miejscu w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="2c566-291">Przykładowe pobieranie zawiera [widok częściowy,](xref:mvc/views/partial) który zawiera poprzednie łącza i te same łącza bez określania obszaru.</span><span class="sxs-lookup"><span data-stu-id="2c566-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="2c566-292">Widok częściowy odwołuje się do [pliku układu,](xref:mvc/views/layout)więc każda strona w aplikacji wyświetla wygenerowane łącza.</span><span class="sxs-lookup"><span data-stu-id="2c566-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2c566-293">Łącza generowane bez określania obszaru są prawidłowe tylko wtedy, gdy odwołuje się do strony w tym samym obszarze i kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2c566-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="2c566-294">Jeśli obszar lub kontroler nie jest określony, routing zależy od wartości *otoczenia.*</span><span class="sxs-lookup"><span data-stu-id="2c566-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="2c566-295">Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy.</span><span class="sxs-lookup"><span data-stu-id="2c566-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2c566-296">W wielu przypadkach dla przykładowej aplikacji przy użyciu wartości otoczenia generuje niepoprawne łącza.</span><span class="sxs-lookup"><span data-stu-id="2c566-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="2c566-297">Aby uzyskać więcej informacji, zobacz [Routing do akcji kontrolera](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="2c566-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="2c566-298">Układ udostępniony dla obszarów przy użyciu pliku _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="2c566-299">Aby udostępnić wspólny układ dla całej aplikacji, przenieś *_ViewStart.cshtml* do folderu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="2c566-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="2c566-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="2c566-301">W swojej standardowej lokalizacji */Views/_ViewImports.cshtml* nie ma zastosowania do obszarów.</span><span class="sxs-lookup"><span data-stu-id="2c566-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="2c566-302">Aby korzystać z `@using`popularnych `@inject` [pomocników znaczników](xref:mvc/views/tag-helpers/intro), lub w twojej okolicy, upewnij się, że odpowiedni plik *_ViewImports.cshtml* [ma zastosowanie do widoków obszaru](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2c566-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="2c566-303">Jeśli chcesz takie samo zachowanie we wszystkich widokach, przenieś */Views/_ViewImports.cshtml* do katalogu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="2c566-304">Zmienianie domyślnego folderu obszaru, w którym są przechowywane widoki</span><span class="sxs-lookup"><span data-stu-id="2c566-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="2c566-305">Poniższy kod zmienia domyślny `"Areas"` `"MyAreas"`folder obszarowy z:</span><span class="sxs-lookup"><span data-stu-id="2c566-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="2c566-306">Obszary ze stronami razor</span><span class="sxs-lookup"><span data-stu-id="2c566-306">Areas with Razor Pages</span></span>

<span data-ttu-id="2c566-307">Obszary ze stronami `Areas/<area name>/Pages` Razor wymagają folderu w katalogu głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="2c566-308">Z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)używana jest następująca struktura folderów:</span><span class="sxs-lookup"><span data-stu-id="2c566-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="2c566-309">Project name (Nazwa projektu)</span><span class="sxs-lookup"><span data-stu-id="2c566-309">Project name</span></span>
  * <span data-ttu-id="2c566-310">Obszary</span><span class="sxs-lookup"><span data-stu-id="2c566-310">Areas</span></span>
    * <span data-ttu-id="2c566-311">Produkty</span><span class="sxs-lookup"><span data-stu-id="2c566-311">Products</span></span>
      * <span data-ttu-id="2c566-312">Strony</span><span class="sxs-lookup"><span data-stu-id="2c566-312">Pages</span></span>
        * <span data-ttu-id="2c566-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="2c566-313">_ViewImports</span></span>
        * <span data-ttu-id="2c566-314">Informacje</span><span class="sxs-lookup"><span data-stu-id="2c566-314">About</span></span>
        * <span data-ttu-id="2c566-315">Indeks</span><span class="sxs-lookup"><span data-stu-id="2c566-315">Index</span></span>
    * <span data-ttu-id="2c566-316">Usługi</span><span class="sxs-lookup"><span data-stu-id="2c566-316">Services</span></span>
      * <span data-ttu-id="2c566-317">Strony</span><span class="sxs-lookup"><span data-stu-id="2c566-317">Pages</span></span>
        * <span data-ttu-id="2c566-318">Zarządzanie</span><span class="sxs-lookup"><span data-stu-id="2c566-318">Manage</span></span>
          * <span data-ttu-id="2c566-319">Informacje</span><span class="sxs-lookup"><span data-stu-id="2c566-319">About</span></span>
          * <span data-ttu-id="2c566-320">Indeks</span><span class="sxs-lookup"><span data-stu-id="2c566-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="2c566-321">Generowanie łączyć się ze stronami i obszarami razor</span><span class="sxs-lookup"><span data-stu-id="2c566-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="2c566-322">Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje generowanie łączy `asp-area="Products"`z określonym obszarem (na przykład):</span><span class="sxs-lookup"><span data-stu-id="2c566-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2c566-323">Łącza wygenerowane za pomocą poprzedniego kodu są prawidłowe w dowolnym miejscu w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="2c566-324">Przykładowe pobieranie zawiera [widok częściowy,](xref:mvc/views/partial) który zawiera poprzednie łącza i te same łącza bez określania obszaru.</span><span class="sxs-lookup"><span data-stu-id="2c566-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="2c566-325">Widok częściowy odwołuje się do [pliku układu,](xref:mvc/views/layout)więc każda strona w aplikacji wyświetla wygenerowane łącza.</span><span class="sxs-lookup"><span data-stu-id="2c566-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2c566-326">Łącza wygenerowane bez określania obszaru są prawidłowe tylko wtedy, gdy odwołuje się do strony w tym samym obszarze.</span><span class="sxs-lookup"><span data-stu-id="2c566-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="2c566-327">Jeśli obszar nie jest określony, routing zależy od wartości *otoczenia.*</span><span class="sxs-lookup"><span data-stu-id="2c566-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="2c566-328">Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy.</span><span class="sxs-lookup"><span data-stu-id="2c566-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2c566-329">W wielu przypadkach dla przykładowej aplikacji przy użyciu wartości otoczenia generuje niepoprawne łącza.</span><span class="sxs-lookup"><span data-stu-id="2c566-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="2c566-330">Rozważmy na przykład łącza wygenerowane na podstawie następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2c566-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="2c566-331">Dla poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="2c566-331">For the preceding code:</span></span>

* <span data-ttu-id="2c566-332">Łącze `<a asp-page="/Manage/About">` wygenerowane z jest poprawne tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze.</span><span class="sxs-lookup"><span data-stu-id="2c566-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="2c566-333">Na przykład `/Services/Manage/` `/Services/Manage/Index`, `/Services/Manage/About`, lub .</span><span class="sxs-lookup"><span data-stu-id="2c566-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="2c566-334">Łącze `<a asp-page="/About">` wygenerowane z jest poprawne tylko wtedy, gdy ostatnie żądanie dotyczyło strony w pliku `/Home`.</span><span class="sxs-lookup"><span data-stu-id="2c566-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="2c566-335">Kod pochodzi z [przykładowego pobrania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="2c566-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="2c566-336">Importowanie obszaru nazw i pomocników znaczników za pomocą pliku _ViewImports</span><span class="sxs-lookup"><span data-stu-id="2c566-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="2c566-337">Plik *_ViewImports.cshtml* można dodać do każdego folderu *Strony* obszaru, aby zaimportować obszar nazw i Pomocników znaczników do każdej strony Razor w folderze.</span><span class="sxs-lookup"><span data-stu-id="2c566-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="2c566-338">Należy wziąć pod uwagę *usługi* obszaru przykładowego kodu, który nie zawiera *pliku _ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="2c566-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2c566-339">Następujące znaczniki pokazuje */Services/Manage/About* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="2c566-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="2c566-340">W poprzednim znaczniku:</span><span class="sxs-lookup"><span data-stu-id="2c566-340">In the preceding markup:</span></span>

* <span data-ttu-id="2c566-341">Do określenia modelu musi służyć w pełni`@model RPareas.Areas.Services.Pages.Manage.AboutModel`kwalifikowana nazwa domeny ( ).</span><span class="sxs-lookup"><span data-stu-id="2c566-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="2c566-342">[Pomocnik tagów](xref:mvc/views/tag-helpers/intro) jest włączona przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="2c566-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="2c566-343">W przykładowym pliku pobierania obszar Produkty zawiera następujący *plik _ViewImports.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="2c566-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="2c566-344">Następujące znaczniki pokazuje */Produkty/ O* Razor Strony:</span><span class="sxs-lookup"><span data-stu-id="2c566-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="2c566-345">W poprzednim pliku obszar nazw `@addTagHelper` i dyrektywa są importowane do pliku przez plik *Obszary/Produkty/Strony/_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="2c566-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="2c566-346">Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika znaczników](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [importowanie dyrektyw udostępnionych](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2c566-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="2c566-347">Układ udostępniony dla obszarów stron razor</span><span class="sxs-lookup"><span data-stu-id="2c566-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="2c566-348">Aby udostępnić wspólny układ dla całej aplikacji, przenieś *_ViewStart.cshtml* do folderu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c566-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="2c566-349">Obszary wydawnicze</span><span class="sxs-lookup"><span data-stu-id="2c566-349">Publishing Areas</span></span>

<span data-ttu-id="2c566-350">Wszystkie pliki i pliki \*.cshtml w katalogu *wwwroot* `<Project Sdk="Microsoft.NET.Sdk.Web">` są publikowane do danych wyjściowych, gdy jest zawarte w pliku \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="2c566-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
