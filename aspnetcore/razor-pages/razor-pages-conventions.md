---
title: Trasy stron brzytwy i konwencje aplikacji w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak konwencje dostawców tras i modeli aplikacji ułatwiają kontrolowanie routingu, odnajdowania i przetwarzania stron.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: f45e327051aba54d1cab67148eb540fb1a5cc149
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667861"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="a7962-103">Trasy stron brzytwy i konwencje aplikacji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7962-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a7962-104">Dowiedz się, jak używać [konwencji routingu i dostawcy modelu aplikacji](xref:mvc/controllers/application-model#conventions) do kontrolowania routingu, odnajdowania i przetwarzania stron w aplikacjach Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a7962-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="a7962-105">Jeśli trzeba skonfigurować niestandardowe trasy stron dla poszczególnych stron, należy skonfigurować routing do stron z [Konwencją AddPageRoute](#configure-a-page-route) opisaną w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="a7962-106">Aby określić trasę strony, dodać segmenty trasy lub dodać parametry `@page` do trasy, użyj dyrektywy strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="a7962-107">Aby uzyskać więcej informacji, zobacz [Trasy niestandardowe](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="a7962-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="a7962-108">Istnieją słowa zastrzeżone, których nie można używać jako segmentów trasy ani nazw parametrów.</span><span class="sxs-lookup"><span data-stu-id="a7962-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="a7962-109">Aby uzyskać więcej informacji, zobacz [Routing: Zarezerwowane nazwy routingu](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="a7962-109">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="a7962-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7962-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="a7962-111">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="a7962-111">Scenario</span></span> | <span data-ttu-id="a7962-112">Przykład pokazuje ...</span><span class="sxs-lookup"><span data-stu-id="a7962-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="a7962-113">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="a7962-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="a7962-114">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="a7962-114">Conventions.Add</span></span><ul><li><span data-ttu-id="a7962-115">IPageRouteModelKonwencja</span><span class="sxs-lookup"><span data-stu-id="a7962-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="a7962-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="a7962-117">IPageHandlerModelKonwencja</span><span class="sxs-lookup"><span data-stu-id="a7962-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="a7962-118">Dodawanie szablonu trasy i nagłówka do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="a7962-119">Konwencje akcji marszruty strony</span><span class="sxs-lookup"><span data-stu-id="a7962-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="a7962-120">DodajfolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="a7962-121">Strona AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="a7962-122">Dodaj stronęProsie</span><span class="sxs-lookup"><span data-stu-id="a7962-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="a7962-123">Dodawanie szablonu trasy do stron w folderze i do jednej strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="a7962-124">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="a7962-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="a7962-125">DodajfolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="a7962-126">Strona AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="a7962-127">ConfigureFilter (klasa filtru, wyrażenie lambda lub fabryka filtrów)</span><span class="sxs-lookup"><span data-stu-id="a7962-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="a7962-128">Dodaj nagłówek do stron w folderze, dodaj nagłówek do jednej strony i skonfiguruj [fabrykę filtrów,](xref:mvc/controllers/filters#ifilterfactory) aby dodać nagłówek do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="a7962-129">Konwencje stron maszynki do golenia <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> są <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> dodawane i konfigurowane przy użyciu metody rozszerzenia do kolekcji usług w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="a7962-129">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="a7962-130">Następujące przykłady konwencji są wyjaśnione w dalszej części tego tematu:</span><span class="sxs-lookup"><span data-stu-id="a7962-130">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="a7962-131">Kolejność marszruty</span><span class="sxs-lookup"><span data-stu-id="a7962-131">Route order</span></span>

<span data-ttu-id="a7962-132">Trasy określają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do przetworzenia (dopasowywanie tras).</span><span class="sxs-lookup"><span data-stu-id="a7962-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="a7962-133">Zamówienie</span><span class="sxs-lookup"><span data-stu-id="a7962-133">Order</span></span>            | <span data-ttu-id="a7962-134">Zachowanie</span><span class="sxs-lookup"><span data-stu-id="a7962-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="a7962-135">-1</span><span class="sxs-lookup"><span data-stu-id="a7962-135">-1</span></span>               | <span data-ttu-id="a7962-136">Trasa jest przetwarzana przed przetworzeniem innych tras.</span><span class="sxs-lookup"><span data-stu-id="a7962-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="a7962-137">0</span><span class="sxs-lookup"><span data-stu-id="a7962-137">0</span></span>                | <span data-ttu-id="a7962-138">Kolejność nie jest określona (wartość domyślna).</span><span class="sxs-lookup"><span data-stu-id="a7962-138">Order isn't specified (default value).</span></span> <span data-ttu-id="a7962-139">Nie `Order` przypisywanie`Order = null`( ) `Order` domyślnie trasy do 0 (zero) do przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="a7962-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="a7962-140">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="a7962-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="a7962-141">Określa kolejność przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="a7962-142">Przetwarzanie trasy jest ustanawiane zgodnie z konwencją:</span><span class="sxs-lookup"><span data-stu-id="a7962-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="a7962-143">Trasy są przetwarzane w kolejności (-1, 0, 1, &hellip; 2, n).</span><span class="sxs-lookup"><span data-stu-id="a7962-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="a7962-144">Gdy trasy mają `Order`to samo , najbardziej konkretna trasa jest dopasowywała najpierw mniej konkretne trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="a7962-145">Gdy trasy o `Order` tej samej i takiej samej liczbie parametrów odpowiadają adresowi URL <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>żądania, trasy są przetwarzane w kolejności, w jakiej są dodawane do pliku .</span><span class="sxs-lookup"><span data-stu-id="a7962-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="a7962-146">Jeśli to możliwe, należy unikać w zależności od ustalonego zamówienia przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="a7962-147">Ogólnie rzecz biorąc routing wybiera prawidłową trasę z dopasowaniem adresu URL.</span><span class="sxs-lookup"><span data-stu-id="a7962-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="a7962-148">Jeśli musisz ustawić `Order` właściwości trasy do routingu żądań poprawnie, schemat routingu aplikacji jest prawdopodobnie mylące dla klientów i kruche do utrzymania.</span><span class="sxs-lookup"><span data-stu-id="a7962-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="a7962-149">Dążyć do uproszczenia schematu routingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="a7962-150">Przykładowa aplikacja wymaga jawnego zamówienia przetwarzania trasy, aby zademonstrować kilka scenariuszy routingu przy użyciu jednej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="a7962-151">Należy jednak podjąć próbę uniknięcia praktyki `Order` ustawiania trasy w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="a7962-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="a7962-152">Routing stron razor i routing kontrolera MVC współużytkują implementację.</span><span class="sxs-lookup"><span data-stu-id="a7962-152">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="a7962-153">Informacje o kolejności marszruty w tematach MVC są dostępne w [serwisie Routing do akcji kontrolera: Zamawianie tras atrybutów](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="a7962-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="a7962-154">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="a7962-154">Model conventions</span></span>

<span data-ttu-id="a7962-155">Dodaj <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pełnomocnika, aby dodać [konwencje modelu,](xref:mvc/controllers/application-model#conventions) które mają zastosowanie do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="a7962-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="a7962-156">Dodawanie konwencji modelu trasy do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="a7962-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="a7962-157">Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu trasy strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="a7962-158">Przykładowa aplikacja `{globalTemplate?}` dodaje szablon trasy do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="a7962-159">Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `1`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a7962-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="a7962-160">Zapewnia to następujące zachowanie dopasowywania trasy w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="a7962-161">Szablon trasy `TheContactPage/{text?}` dla jest dodawany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="a7962-162">Trasa strony kontaktu ma domyślną kolejność `null` (`Order = 0`), `{globalTemplate?}` więc jest zgodna przed szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="a7962-163">Szablon `{aboutTemplate?}` trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="a7962-164">Szablon `{aboutTemplate?}` otrzymuje . `Order` `2`</span><span class="sxs-lookup"><span data-stu-id="a7962-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="a7962-165">Gdy strona Informacje jest `/About/RouteDataValue`wymagane w , "RouteDataValue" jest `RouteData.Values["aboutTemplate"]` `Order = 2`ładowany do `Order` `RouteData.Values["globalTemplate"]` (`Order = 1`) i nie ( ) z powodu ustawienia właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="a7962-166">Szablon `{otherPagesTemplate?}` trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="a7962-167">Szablon `{otherPagesTemplate?}` otrzymuje . `Order` `2`</span><span class="sxs-lookup"><span data-stu-id="a7962-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="a7962-168">Gdy dowolna strona w folderze *Pages/OtherPages* jest żądana z parametrem trasy (na `/OtherPages/Page1/RouteDataValue` `RouteData.Values["globalTemplate"]` przykład), "RouteDataValue" jest ładowany`Order = 1`do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="a7962-169">Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje.</span><span class="sxs-lookup"><span data-stu-id="a7962-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="a7962-170">Polegaj na routingu, aby wybrać właściwą trasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="a7962-171">Opcje stron maszynki do <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>golenia, takie jak dodawanie , `Startup.ConfigureServices`są dodawane po dodaniu MVC do kolekcji usługi w pliku .</span><span class="sxs-lookup"><span data-stu-id="a7962-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a7962-172">Na przykład zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="a7962-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="a7962-173">Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue` o i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Strona Informacje jest żądana z segmentem trasy GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="a7962-176">Dodawanie konwencji modelu aplikacji do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="a7962-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="a7962-177">Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas tworzenia modelu aplikacji strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="a7962-178">Aby zademonstrować tę i inne konwencje w dalszej części tematu, przykładowa aplikacja zawiera `AddHeaderAttribute` klasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="a7962-179">Konstruktor klasy akceptuje `name` zestaw `values` ciągów i tablicy ciągów.</span><span class="sxs-lookup"><span data-stu-id="a7962-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="a7962-180">Te wartości są `OnResultExecuting` używane w jego metody, aby ustawić nagłówek odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a7962-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="a7962-181">Pełna klasa jest wyświetlana w sekcji [Konwencje akcji modelu strony](#page-model-action-conventions) w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="a7962-182">Przykładowa aplikacja używa `AddHeaderAttribute` klasy, aby `GlobalHeader`dodać nagłówek, do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="a7962-183">*Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="a7962-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="a7962-184">Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie Informacje pokazują, że globalheader został dodany.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="a7962-186">Dodawanie konwencji modelu programu obsługi do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="a7962-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="a7962-187">Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu programu obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="a7962-188">*Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="a7962-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="a7962-189">Konwencje akcji marszruty strony</span><span class="sxs-lookup"><span data-stu-id="a7962-189">Page route action conventions</span></span>

<span data-ttu-id="a7962-190">Domyślny dostawca modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> który wywodzi się z konwencji wywoływania, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania tras stron.</span><span class="sxs-lookup"><span data-stu-id="a7962-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="a7962-191">Konwencja modelu trasy folderu</span><span class="sxs-lookup"><span data-stu-id="a7962-191">Folder route model convention</span></span>

<span data-ttu-id="a7962-192">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na wszystkich stronach w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="a7962-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="a7962-193">Przykładowa aplikacja <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> służy do `{otherPagesTemplate?}` dodawania szablonu trasy do stron w folderze *OtherPages:*</span><span class="sxs-lookup"><span data-stu-id="a7962-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="a7962-194">Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a7962-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="a7962-195">Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="a7962-196">Jeśli strona w folderze *Pages/OtherPages* jest żądana z wartością parametru trasy (na `/OtherPages/Page1/RouteDataValue`przykład), `RouteData.Values["globalTemplate"]` `Order = 1`"RouteDataValue" jest ładowany do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="a7962-197">Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje.</span><span class="sxs-lookup"><span data-stu-id="a7962-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="a7962-198">Polegaj na routingu, aby wybrać właściwą trasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="a7962-199">Poproś o przykładową stronę `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` Page1 i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Strona1 w folderze OtherPages jest żądana z segmentem trasy GlobalRouteValue i OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="a7962-202">Konwencja modelu trasy strony</span><span class="sxs-lookup"><span data-stu-id="a7962-202">Page route model convention</span></span>

<span data-ttu-id="a7962-203">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na stronie o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="a7962-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="a7962-204">Przykładowa aplikacja `AddPageRouteModelConvention` służy do `{aboutTemplate?}` dodawania szablonu trasy do strony Informacje:</span><span class="sxs-lookup"><span data-stu-id="a7962-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="a7962-205">Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a7962-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="a7962-206">Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="a7962-207">Jeśli strona Informacje jest żądana z `/About/RouteDataValue`wartością parametru trasy w , `RouteData.Values["globalTemplate"]` "RouteDataValue" jest ładowany do (`Order = 1`) i nie `RouteData.Values["aboutTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="a7962-208">Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje.</span><span class="sxs-lookup"><span data-stu-id="a7962-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="a7962-209">Polegaj na routingu, aby wybrać właściwą trasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="a7962-210">Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue/AboutRouteValue` o i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Strona Informacje jest żądana z segmentami trasy dla GlobalRouteValue i AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="a7962-213">Dostosowywanie tras stron za pomocą transformatora parametrów</span><span class="sxs-lookup"><span data-stu-id="a7962-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="a7962-214">Trasy strony generowane przez ASP.NET Core można dostosować za pomocą transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="a7962-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="a7962-215">Transformator parametrów `IOutboundParameterTransformer` implementuje i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="a7962-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="a7962-216">Na przykład transformator parametrów `SlugifyParameterTransformer` `SubscriptionManagement` niestandardowych `subscription-management`zmienia wartość trasy na .</span><span class="sxs-lookup"><span data-stu-id="a7962-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="a7962-217">Konwencja `PageRouteTransformerConvention` modelu trasy strony stosuje transformator parametrów do segmentów folderu i nazw plików automatycznie generowanych tras stron w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="a7962-218">Na przykład plik Razor Pages w */Pages/SubscriptionManagement/ViewAll.cshtml* miałby `/SubscriptionManagement/ViewAll` swoją `/subscription-management/view-all`trasę przepisaną z do .</span><span class="sxs-lookup"><span data-stu-id="a7962-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="a7962-219">`PageRouteTransformerConvention`przekształca tylko automatycznie generowane segmenty trasy strony, które pochodzą z folderu Razor Pages i nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="a7962-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="a7962-220">Nie przekształca segmentów trasy dodanych `@page` wraz z dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="a7962-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="a7962-221">Konwencja nie przekształca również tras <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>dodanych przez .</span><span class="sxs-lookup"><span data-stu-id="a7962-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="a7962-222">Jest `PageRouteTransformerConvention` zarejestrowany jako opcja `Startup.ConfigureServices`w:</span><span class="sxs-lookup"><span data-stu-id="a7962-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a><span data-ttu-id="a7962-223">Konfigurowanie trasy strony</span><span class="sxs-lookup"><span data-stu-id="a7962-223">Configure a page route</span></span>

<span data-ttu-id="a7962-224">Służy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> do konfigurowania trasy do strony w określonej ścieżce strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="a7962-225">Wygenerowane łącza do strony używają określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="a7962-226">`AddPageRoute`do `AddPageRouteModelConvention` ustalenia trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="a7962-227">Przykładowa aplikacja tworzy `/TheContactPage` trasę do *contact.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a7962-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="a7962-228">Do strony Kontakt można `/Contact` również uzyskać pod adresem za pomocą jej domyślnej trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="a7962-229">Niestandardowa trasa aplikacji przykładowej do strony Kontakt `text` umożliwia`{text?}`opcjonalny segment trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="a7962-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="a7962-230">Strona zawiera również ten opcjonalny segment w swojej `@page` dyrektywie `/Contact` w przypadku, gdy użytkownik uzyskuje dostęp do strony na swojej trasie:</span><span class="sxs-lookup"><span data-stu-id="a7962-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="a7962-231">Należy zauważyć, że adres URL wygenerowany dla łącza **Kontakt** na renderowanej stronie odzwierciedla zaktualizowaną trasę:</span><span class="sxs-lookup"><span data-stu-id="a7962-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Przykładowe łącze kontaktowe aplikacji na pasku nawigacyjnym](razor-pages-conventions/_static/contact-link.png)

![Sprawdzenie łącza Kontakt w renderowanym kodzie HTML wskazuje, że href jest ustawiony na "/TheContactPage"](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="a7962-234">Odwiedź stronę Kontakt przy zwykłej `/Contact`trasie lub trasie `/TheContactPage`niestandardowej .</span><span class="sxs-lookup"><span data-stu-id="a7962-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="a7962-235">Jeśli podasz `text` dodatkowy segment trasy, strona pokazuje segment zakodowany w formacie HTML, który podajesz:</span><span class="sxs-lookup"><span data-stu-id="a7962-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Przykład przeglądarki krawędzi dostarczania opcjonalnego segmentu trasy "tekst" "TextValue" w adresie URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="a7962-238">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="a7962-238">Page model action conventions</span></span>

<span data-ttu-id="a7962-239">Domyślny dostawca modelu strony, który implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> wywoływania konwencji, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania modeli stron.</span><span class="sxs-lookup"><span data-stu-id="a7962-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="a7962-240">Konwencje te są przydatne podczas tworzenia i modyfikowania scenariuszy odnajdowania i przetwarzania strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="a7962-241">W przykładach w tej sekcji przykładowa `AddHeaderAttribute` aplikacja używa klasy, która jest <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, która stosuje nagłówek odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="a7962-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="a7962-242">Korzystając z konwencji, w przykładzie pokazano, jak zastosować atrybut do wszystkich stron w folderze i do jednej strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="a7962-243">**Konwencja modelu aplikacji folderu**</span><span class="sxs-lookup"><span data-stu-id="a7962-243">**Folder app model convention**</span></span>

<span data-ttu-id="a7962-244">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> wywołującej akcję dla wystąpień dla wszystkich stron w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="a7962-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="a7962-245">Przykład pokazuje użycie `AddFolderApplicationModelConvention` przez dodanie nagłówka, `OtherPagesHeader`do stron wewnątrz *folderu OtherPages* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="a7962-246">Poproś przykładową stronę Page1 `localhost:5000/OtherPages/Page1` i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi strony OtherPages/Page1 pokazują, że dodano otherpagesheader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="a7962-248">**Konwencja modelu aplikacji strony**</span><span class="sxs-lookup"><span data-stu-id="a7962-248">**Page app model convention**</span></span>

<span data-ttu-id="a7962-249">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> która wywołuje akcję na stronie o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="a7962-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="a7962-250">Przykład pokazuje użycie `AddPageApplicationModelConvention` przez dodanie nagłówka, `AboutHeader`do Informacje strony:</span><span class="sxs-lookup"><span data-stu-id="a7962-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="a7962-251">Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie Informacje pokazują, że aboutheader został dodany.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="a7962-253">**Konfigurowanie filtru**</span><span class="sxs-lookup"><span data-stu-id="a7962-253">**Configure a filter**</span></span>

<span data-ttu-id="a7962-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określony filtr do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="a7962-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="a7962-255">Można zaimplementować klasę filtru, ale przykładowa aplikacja pokazuje, jak zaimplementować filtr w wyrażeniu lambda, który jest implementowany za kulisami jako fabryka, która zwraca filtr:</span><span class="sxs-lookup"><span data-stu-id="a7962-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="a7962-256">Model aplikacji strony służy do sprawdzania ścieżki względnej dla segmentów prowadzących do strony Page2 w folderze *OtherPages.*</span><span class="sxs-lookup"><span data-stu-id="a7962-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="a7962-257">Jeśli warunek przejdzie, zostanie dodany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="a7962-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="a7962-258">Jeśli nie, `EmptyFilter` jest stosowany.</span><span class="sxs-lookup"><span data-stu-id="a7962-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="a7962-259">`EmptyFilter`jest [filtrem akcji](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="a7962-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="a7962-260">Ponieważ filtry akcji są ignorowane przez `EmptyFilter` strony Razor Pages, nie ma `OtherPages/Page2`wpływu zgodnie z przeznaczeniem, jeśli ścieżka nie zawiera .</span><span class="sxs-lookup"><span data-stu-id="a7962-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="a7962-261">Poproś przykładową stronę Page2 `localhost:5000/OtherPages/Page2` o pozycję Page2 i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header zostanie dodany do odpowiedzi dla Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="a7962-263">**Konfigurowanie fabryki filtrów**</span><span class="sxs-lookup"><span data-stu-id="a7962-263">**Configure a filter factory**</span></span>

<span data-ttu-id="a7962-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określoną fabrykę, aby zastosować [filtry](xref:mvc/controllers/filters) do wszystkich stron Razor.</span><span class="sxs-lookup"><span data-stu-id="a7962-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="a7962-265">Przykładowa aplikacja zawiera przykład użycia [fabryki filtrów](xref:mvc/controllers/filters#ifilterfactory) `FilterFactoryHeader`przez dodanie nagłówka, z dwiema wartościami do stron aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="a7962-266">*AddHeaderWithFactory.cs:*</span><span class="sxs-lookup"><span data-stu-id="a7962-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="a7962-267">Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie Informacje pokazują, że dodano dwa nagłówki FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="a7962-269">Filtry MVC i filtr strony (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="a7962-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="a7962-270">[Filtry akcji](xref:mvc/controllers/filters#action-filters) MVC są ignorowane przez strony Razor Pages, ponieważ razor Pages używają metod obsługi.</span><span class="sxs-lookup"><span data-stu-id="a7962-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="a7962-271">Dostępne są inne typy filtrów MVC: [Autoryzacja,](xref:mvc/controllers/filters#authorization-filters) [Wyjątek,](xref:mvc/controllers/filters#exception-filters) [Zasób](xref:mvc/controllers/filters#resource-filters)i [Wynik](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="a7962-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="a7962-272">Aby uzyskać więcej informacji, zobacz temat [Filtry.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="a7962-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="a7962-273">Filtr Strony<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) to filtr, który ma zastosowanie do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="a7962-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="a7962-274">Aby uzyskać więcej informacji, zobacz [Metody filtrowania stron razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a7962-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7962-275">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a7962-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a7962-276">Dowiedz się, jak używać [konwencji routingu i dostawcy modelu aplikacji](xref:mvc/controllers/application-model#conventions) do kontrolowania routingu, odnajdowania i przetwarzania stron w aplikacjach Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a7962-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="a7962-277">Jeśli trzeba skonfigurować niestandardowe trasy stron dla poszczególnych stron, należy skonfigurować routing do stron z [Konwencją AddPageRoute](#configure-a-page-route) opisaną w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="a7962-278">Aby określić trasę strony, dodać segmenty trasy lub dodać parametry `@page` do trasy, użyj dyrektywy strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="a7962-279">Aby uzyskać więcej informacji, zobacz [Trasy niestandardowe](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="a7962-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="a7962-280">Istnieją słowa zastrzeżone, których nie można używać jako segmentów trasy ani nazw parametrów.</span><span class="sxs-lookup"><span data-stu-id="a7962-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="a7962-281">Aby uzyskać więcej informacji, zobacz [Routing: Zarezerwowane nazwy routingu](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="a7962-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="a7962-282">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7962-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="a7962-283">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="a7962-283">Scenario</span></span> | <span data-ttu-id="a7962-284">Przykład pokazuje ...</span><span class="sxs-lookup"><span data-stu-id="a7962-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="a7962-285">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="a7962-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="a7962-286">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="a7962-286">Conventions.Add</span></span><ul><li><span data-ttu-id="a7962-287">IPageRouteModelKonwencja</span><span class="sxs-lookup"><span data-stu-id="a7962-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="a7962-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="a7962-289">IPageHandlerModelKonwencja</span><span class="sxs-lookup"><span data-stu-id="a7962-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="a7962-290">Dodawanie szablonu trasy i nagłówka do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="a7962-291">Konwencje akcji marszruty strony</span><span class="sxs-lookup"><span data-stu-id="a7962-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="a7962-292">DodajfolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="a7962-293">Strona AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="a7962-294">Dodaj stronęProsie</span><span class="sxs-lookup"><span data-stu-id="a7962-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="a7962-295">Dodawanie szablonu trasy do stron w folderze i do jednej strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="a7962-296">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="a7962-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="a7962-297">DodajfolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="a7962-298">Strona AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="a7962-299">ConfigureFilter (klasa filtru, wyrażenie lambda lub fabryka filtrów)</span><span class="sxs-lookup"><span data-stu-id="a7962-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="a7962-300">Dodaj nagłówek do stron w folderze, dodaj nagłówek do jednej strony i skonfiguruj [fabrykę filtrów,](xref:mvc/controllers/filters#ifilterfactory) aby dodać nagłówek do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="a7962-301">Konwencje stron maszynki do golenia <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> są <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> dodawane i konfigurowane przy użyciu metody rozszerzenia do kolekcji usług w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="a7962-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="a7962-302">Następujące przykłady konwencji są wyjaśnione w dalszej części tego tematu:</span><span class="sxs-lookup"><span data-stu-id="a7962-302">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="a7962-303">Kolejność marszruty</span><span class="sxs-lookup"><span data-stu-id="a7962-303">Route order</span></span>

<span data-ttu-id="a7962-304">Trasy określają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do przetworzenia (dopasowywanie tras).</span><span class="sxs-lookup"><span data-stu-id="a7962-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="a7962-305">Zamówienie</span><span class="sxs-lookup"><span data-stu-id="a7962-305">Order</span></span>            | <span data-ttu-id="a7962-306">Zachowanie</span><span class="sxs-lookup"><span data-stu-id="a7962-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="a7962-307">-1</span><span class="sxs-lookup"><span data-stu-id="a7962-307">-1</span></span>               | <span data-ttu-id="a7962-308">Trasa jest przetwarzana przed przetworzeniem innych tras.</span><span class="sxs-lookup"><span data-stu-id="a7962-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="a7962-309">0</span><span class="sxs-lookup"><span data-stu-id="a7962-309">0</span></span>                | <span data-ttu-id="a7962-310">Kolejność nie jest określona (wartość domyślna).</span><span class="sxs-lookup"><span data-stu-id="a7962-310">Order isn't specified (default value).</span></span> <span data-ttu-id="a7962-311">Nie `Order` przypisywanie`Order = null`( ) `Order` domyślnie trasy do 0 (zero) do przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="a7962-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="a7962-312">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="a7962-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="a7962-313">Określa kolejność przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="a7962-314">Przetwarzanie trasy jest ustanawiane zgodnie z konwencją:</span><span class="sxs-lookup"><span data-stu-id="a7962-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="a7962-315">Trasy są przetwarzane w kolejności (-1, 0, 1, &hellip; 2, n).</span><span class="sxs-lookup"><span data-stu-id="a7962-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="a7962-316">Gdy trasy mają `Order`to samo , najbardziej konkretna trasa jest dopasowywała najpierw mniej konkretne trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="a7962-317">Gdy trasy o `Order` tej samej i takiej samej liczbie parametrów odpowiadają adresowi URL <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>żądania, trasy są przetwarzane w kolejności, w jakiej są dodawane do pliku .</span><span class="sxs-lookup"><span data-stu-id="a7962-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="a7962-318">Jeśli to możliwe, należy unikać w zależności od ustalonego zamówienia przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="a7962-319">Ogólnie rzecz biorąc routing wybiera prawidłową trasę z dopasowaniem adresu URL.</span><span class="sxs-lookup"><span data-stu-id="a7962-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="a7962-320">Jeśli musisz ustawić `Order` właściwości trasy do routingu żądań poprawnie, schemat routingu aplikacji jest prawdopodobnie mylące dla klientów i kruche do utrzymania.</span><span class="sxs-lookup"><span data-stu-id="a7962-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="a7962-321">Dążyć do uproszczenia schematu routingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="a7962-322">Przykładowa aplikacja wymaga jawnego zamówienia przetwarzania trasy, aby zademonstrować kilka scenariuszy routingu przy użyciu jednej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="a7962-323">Należy jednak podjąć próbę uniknięcia praktyki `Order` ustawiania trasy w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="a7962-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="a7962-324">Routing stron razor i routing kontrolera MVC współużytkują implementację.</span><span class="sxs-lookup"><span data-stu-id="a7962-324">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="a7962-325">Informacje o kolejności marszruty w tematach MVC są dostępne w [serwisie Routing do akcji kontrolera: Zamawianie tras atrybutów](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="a7962-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="a7962-326">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="a7962-326">Model conventions</span></span>

<span data-ttu-id="a7962-327">Dodaj <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pełnomocnika, aby dodać [konwencje modelu,](xref:mvc/controllers/application-model#conventions) które mają zastosowanie do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="a7962-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="a7962-328">Dodawanie konwencji modelu trasy do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="a7962-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="a7962-329">Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu trasy strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="a7962-330">Przykładowa aplikacja `{globalTemplate?}` dodaje szablon trasy do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="a7962-331">Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `1`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a7962-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="a7962-332">Zapewnia to następujące zachowanie dopasowywania trasy w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="a7962-333">Szablon trasy `TheContactPage/{text?}` dla jest dodawany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="a7962-334">Trasa strony kontaktu ma domyślną kolejność `null` (`Order = 0`), `{globalTemplate?}` więc jest zgodna przed szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="a7962-335">Szablon `{aboutTemplate?}` trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="a7962-336">Szablon `{aboutTemplate?}` otrzymuje . `Order` `2`</span><span class="sxs-lookup"><span data-stu-id="a7962-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="a7962-337">Gdy strona Informacje jest `/About/RouteDataValue`wymagane w , "RouteDataValue" jest `RouteData.Values["aboutTemplate"]` `Order = 2`ładowany do `Order` `RouteData.Values["globalTemplate"]` (`Order = 1`) i nie ( ) z powodu ustawienia właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="a7962-338">Szablon `{otherPagesTemplate?}` trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="a7962-339">Szablon `{otherPagesTemplate?}` otrzymuje . `Order` `2`</span><span class="sxs-lookup"><span data-stu-id="a7962-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="a7962-340">Gdy dowolna strona w folderze *Pages/OtherPages* jest żądana z parametrem trasy (na `/OtherPages/Page1/RouteDataValue` `RouteData.Values["globalTemplate"]` przykład), "RouteDataValue" jest ładowany`Order = 1`do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="a7962-341">Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje.</span><span class="sxs-lookup"><span data-stu-id="a7962-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="a7962-342">Polegaj na routingu, aby wybrać właściwą trasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="a7962-343">Opcje stron maszynki do <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>golenia, takie jak dodawanie , `Startup.ConfigureServices`są dodawane po dodaniu MVC do kolekcji usługi w pliku .</span><span class="sxs-lookup"><span data-stu-id="a7962-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a7962-344">Na przykład zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="a7962-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="a7962-345">Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue` o i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Strona Informacje jest żądana z segmentem trasy GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="a7962-348">Dodawanie konwencji modelu aplikacji do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="a7962-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="a7962-349">Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas tworzenia modelu aplikacji strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="a7962-350">Aby zademonstrować tę i inne konwencje w dalszej części tematu, przykładowa aplikacja zawiera `AddHeaderAttribute` klasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="a7962-351">Konstruktor klasy akceptuje `name` zestaw `values` ciągów i tablicy ciągów.</span><span class="sxs-lookup"><span data-stu-id="a7962-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="a7962-352">Te wartości są `OnResultExecuting` używane w jego metody, aby ustawić nagłówek odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a7962-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="a7962-353">Pełna klasa jest wyświetlana w sekcji [Konwencje akcji modelu strony](#page-model-action-conventions) w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="a7962-354">Przykładowa aplikacja używa `AddHeaderAttribute` klasy, aby `GlobalHeader`dodać nagłówek, do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="a7962-355">*Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="a7962-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="a7962-356">Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie Informacje pokazują, że globalheader został dodany.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="a7962-358">Dodawanie konwencji modelu programu obsługi do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="a7962-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="a7962-359">Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu programu obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="a7962-360">*Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="a7962-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="a7962-361">Konwencje akcji marszruty strony</span><span class="sxs-lookup"><span data-stu-id="a7962-361">Page route action conventions</span></span>

<span data-ttu-id="a7962-362">Domyślny dostawca modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> który wywodzi się z konwencji wywoływania, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania tras stron.</span><span class="sxs-lookup"><span data-stu-id="a7962-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="a7962-363">Konwencja modelu trasy folderu</span><span class="sxs-lookup"><span data-stu-id="a7962-363">Folder route model convention</span></span>

<span data-ttu-id="a7962-364">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na wszystkich stronach w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="a7962-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="a7962-365">Przykładowa aplikacja <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> służy do `{otherPagesTemplate?}` dodawania szablonu trasy do stron w folderze *OtherPages:*</span><span class="sxs-lookup"><span data-stu-id="a7962-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="a7962-366">Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a7962-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="a7962-367">Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="a7962-368">Jeśli strona w folderze *Pages/OtherPages* jest żądana z wartością parametru trasy (na `/OtherPages/Page1/RouteDataValue`przykład), `RouteData.Values["globalTemplate"]` `Order = 1`"RouteDataValue" jest ładowany do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="a7962-369">Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje.</span><span class="sxs-lookup"><span data-stu-id="a7962-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="a7962-370">Polegaj na routingu, aby wybrać właściwą trasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="a7962-371">Poproś o przykładową stronę `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` Page1 i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Strona1 w folderze OtherPages jest żądana z segmentem trasy GlobalRouteValue i OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="a7962-374">Konwencja modelu trasy strony</span><span class="sxs-lookup"><span data-stu-id="a7962-374">Page route model convention</span></span>

<span data-ttu-id="a7962-375">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na stronie o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="a7962-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="a7962-376">Przykładowa aplikacja `AddPageRouteModelConvention` służy do `{aboutTemplate?}` dodawania szablonu trasy do strony Informacje:</span><span class="sxs-lookup"><span data-stu-id="a7962-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="a7962-377">Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a7962-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="a7962-378">Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="a7962-379">Jeśli strona Informacje jest żądana z `/About/RouteDataValue`wartością parametru trasy w , `RouteData.Values["globalTemplate"]` "RouteDataValue" jest ładowany do (`Order = 1`) i nie `RouteData.Values["aboutTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="a7962-380">Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje.</span><span class="sxs-lookup"><span data-stu-id="a7962-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="a7962-381">Polegaj na routingu, aby wybrać właściwą trasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="a7962-382">Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue/AboutRouteValue` o i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Strona Informacje jest żądana z segmentami trasy dla GlobalRouteValue i AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="a7962-385">Dostosowywanie tras stron za pomocą transformatora parametrów</span><span class="sxs-lookup"><span data-stu-id="a7962-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="a7962-386">Trasy strony generowane przez ASP.NET Core można dostosować za pomocą transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="a7962-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="a7962-387">Transformator parametrów `IOutboundParameterTransformer` implementuje i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="a7962-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="a7962-388">Na przykład transformator parametrów `SlugifyParameterTransformer` `SubscriptionManagement` niestandardowych `subscription-management`zmienia wartość trasy na .</span><span class="sxs-lookup"><span data-stu-id="a7962-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="a7962-389">Konwencja `PageRouteTransformerConvention` modelu trasy strony stosuje transformator parametrów do segmentów folderu i nazw plików automatycznie generowanych tras stron w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="a7962-390">Na przykład plik Razor Pages w */Pages/SubscriptionManagement/ViewAll.cshtml* miałby `/SubscriptionManagement/ViewAll` swoją `/subscription-management/view-all`trasę przepisaną z do .</span><span class="sxs-lookup"><span data-stu-id="a7962-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="a7962-391">`PageRouteTransformerConvention`przekształca tylko automatycznie generowane segmenty trasy strony, które pochodzą z folderu Razor Pages i nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="a7962-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="a7962-392">Nie przekształca segmentów trasy dodanych `@page` wraz z dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="a7962-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="a7962-393">Konwencja nie przekształca również tras <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>dodanych przez .</span><span class="sxs-lookup"><span data-stu-id="a7962-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="a7962-394">Jest `PageRouteTransformerConvention` zarejestrowany jako opcja `Startup.ConfigureServices`w:</span><span class="sxs-lookup"><span data-stu-id="a7962-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a><span data-ttu-id="a7962-395">Konfigurowanie trasy strony</span><span class="sxs-lookup"><span data-stu-id="a7962-395">Configure a page route</span></span>

<span data-ttu-id="a7962-396">Służy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> do konfigurowania trasy do strony w określonej ścieżce strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="a7962-397">Wygenerowane łącza do strony używają określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="a7962-398">`AddPageRoute`do `AddPageRouteModelConvention` ustalenia trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="a7962-399">Przykładowa aplikacja tworzy `/TheContactPage` trasę do *contact.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a7962-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="a7962-400">Do strony Kontakt można `/Contact` również uzyskać pod adresem za pomocą jej domyślnej trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="a7962-401">Niestandardowa trasa aplikacji przykładowej do strony Kontakt `text` umożliwia`{text?}`opcjonalny segment trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="a7962-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="a7962-402">Strona zawiera również ten opcjonalny segment w swojej `@page` dyrektywie `/Contact` w przypadku, gdy użytkownik uzyskuje dostęp do strony na swojej trasie:</span><span class="sxs-lookup"><span data-stu-id="a7962-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="a7962-403">Należy zauważyć, że adres URL wygenerowany dla łącza **Kontakt** na renderowanej stronie odzwierciedla zaktualizowaną trasę:</span><span class="sxs-lookup"><span data-stu-id="a7962-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Przykładowe łącze kontaktowe aplikacji na pasku nawigacyjnym](razor-pages-conventions/_static/contact-link.png)

![Sprawdzenie łącza Kontakt w renderowanym kodzie HTML wskazuje, że href jest ustawiony na "/TheContactPage"](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="a7962-406">Odwiedź stronę Kontakt przy zwykłej `/Contact`trasie lub trasie `/TheContactPage`niestandardowej .</span><span class="sxs-lookup"><span data-stu-id="a7962-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="a7962-407">Jeśli podasz `text` dodatkowy segment trasy, strona pokazuje segment zakodowany w formacie HTML, który podajesz:</span><span class="sxs-lookup"><span data-stu-id="a7962-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Przykład przeglądarki krawędzi dostarczania opcjonalnego segmentu trasy "tekst" "TextValue" w adresie URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="a7962-410">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="a7962-410">Page model action conventions</span></span>

<span data-ttu-id="a7962-411">Domyślny dostawca modelu strony, który implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> wywoływania konwencji, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania modeli stron.</span><span class="sxs-lookup"><span data-stu-id="a7962-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="a7962-412">Konwencje te są przydatne podczas tworzenia i modyfikowania scenariuszy odnajdowania i przetwarzania strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="a7962-413">W przykładach w tej sekcji przykładowa `AddHeaderAttribute` aplikacja używa klasy, która jest <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, która stosuje nagłówek odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="a7962-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="a7962-414">Korzystając z konwencji, w przykładzie pokazano, jak zastosować atrybut do wszystkich stron w folderze i do jednej strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="a7962-415">**Konwencja modelu aplikacji folderu**</span><span class="sxs-lookup"><span data-stu-id="a7962-415">**Folder app model convention**</span></span>

<span data-ttu-id="a7962-416">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> wywołującej akcję dla wystąpień dla wszystkich stron w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="a7962-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="a7962-417">Przykład pokazuje użycie `AddFolderApplicationModelConvention` przez dodanie nagłówka, `OtherPagesHeader`do stron wewnątrz *folderu OtherPages* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="a7962-418">Poproś przykładową stronę Page1 `localhost:5000/OtherPages/Page1` i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi strony OtherPages/Page1 pokazują, że dodano otherpagesheader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="a7962-420">**Konwencja modelu aplikacji strony**</span><span class="sxs-lookup"><span data-stu-id="a7962-420">**Page app model convention**</span></span>

<span data-ttu-id="a7962-421">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> która wywołuje akcję na stronie o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="a7962-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="a7962-422">Przykład pokazuje użycie `AddPageApplicationModelConvention` przez dodanie nagłówka, `AboutHeader`do Informacje strony:</span><span class="sxs-lookup"><span data-stu-id="a7962-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="a7962-423">Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie Informacje pokazują, że aboutheader został dodany.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="a7962-425">**Konfigurowanie filtru**</span><span class="sxs-lookup"><span data-stu-id="a7962-425">**Configure a filter**</span></span>

<span data-ttu-id="a7962-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określony filtr do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="a7962-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="a7962-427">Można zaimplementować klasę filtru, ale przykładowa aplikacja pokazuje, jak zaimplementować filtr w wyrażeniu lambda, który jest implementowany za kulisami jako fabryka, która zwraca filtr:</span><span class="sxs-lookup"><span data-stu-id="a7962-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="a7962-428">Model aplikacji strony służy do sprawdzania ścieżki względnej dla segmentów prowadzących do strony Page2 w folderze *OtherPages.*</span><span class="sxs-lookup"><span data-stu-id="a7962-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="a7962-429">Jeśli warunek przejdzie, zostanie dodany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="a7962-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="a7962-430">Jeśli nie, `EmptyFilter` jest stosowany.</span><span class="sxs-lookup"><span data-stu-id="a7962-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="a7962-431">`EmptyFilter`jest [filtrem akcji](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="a7962-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="a7962-432">Ponieważ filtry akcji są ignorowane przez `EmptyFilter` strony Razor Pages, nie ma `OtherPages/Page2`wpływu zgodnie z przeznaczeniem, jeśli ścieżka nie zawiera .</span><span class="sxs-lookup"><span data-stu-id="a7962-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="a7962-433">Poproś przykładową stronę Page2 `localhost:5000/OtherPages/Page2` o pozycję Page2 i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header zostanie dodany do odpowiedzi dla Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="a7962-435">**Konfigurowanie fabryki filtrów**</span><span class="sxs-lookup"><span data-stu-id="a7962-435">**Configure a filter factory**</span></span>

<span data-ttu-id="a7962-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określoną fabrykę, aby zastosować [filtry](xref:mvc/controllers/filters) do wszystkich stron Razor.</span><span class="sxs-lookup"><span data-stu-id="a7962-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="a7962-437">Przykładowa aplikacja zawiera przykład użycia [fabryki filtrów](xref:mvc/controllers/filters#ifilterfactory) `FilterFactoryHeader`przez dodanie nagłówka, z dwiema wartościami do stron aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="a7962-438">*AddHeaderWithFactory.cs:*</span><span class="sxs-lookup"><span data-stu-id="a7962-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="a7962-439">Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie Informacje pokazują, że dodano dwa nagłówki FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="a7962-441">Filtry MVC i filtr strony (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="a7962-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="a7962-442">[Filtry akcji](xref:mvc/controllers/filters#action-filters) MVC są ignorowane przez strony Razor Pages, ponieważ razor Pages używają metod obsługi.</span><span class="sxs-lookup"><span data-stu-id="a7962-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="a7962-443">Dostępne są inne typy filtrów MVC: [Autoryzacja,](xref:mvc/controllers/filters#authorization-filters) [Wyjątek,](xref:mvc/controllers/filters#exception-filters) [Zasób](xref:mvc/controllers/filters#resource-filters)i [Wynik](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="a7962-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="a7962-444">Aby uzyskać więcej informacji, zobacz temat [Filtry.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="a7962-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="a7962-445">Filtr Strony<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) to filtr, który ma zastosowanie do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="a7962-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="a7962-446">Aby uzyskać więcej informacji, zobacz [Metody filtrowania stron razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a7962-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7962-447">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a7962-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a7962-448">Dowiedz się, jak używać [konwencji routingu i dostawcy modelu aplikacji](xref:mvc/controllers/application-model#conventions) do kontrolowania routingu, odnajdowania i przetwarzania stron w aplikacjach Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a7962-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="a7962-449">Jeśli trzeba skonfigurować niestandardowe trasy stron dla poszczególnych stron, należy skonfigurować routing do stron z [Konwencją AddPageRoute](#configure-a-page-route) opisaną w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="a7962-450">Aby określić trasę strony, dodać segmenty trasy lub dodać parametry `@page` do trasy, użyj dyrektywy strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="a7962-451">Aby uzyskać więcej informacji, zobacz [Trasy niestandardowe](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="a7962-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="a7962-452">Istnieją słowa zastrzeżone, których nie można używać jako segmentów trasy ani nazw parametrów.</span><span class="sxs-lookup"><span data-stu-id="a7962-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="a7962-453">Aby uzyskać więcej informacji, zobacz [Routing: Zarezerwowane nazwy routingu](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="a7962-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="a7962-454">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7962-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="a7962-455">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="a7962-455">Scenario</span></span> | <span data-ttu-id="a7962-456">Przykład pokazuje ...</span><span class="sxs-lookup"><span data-stu-id="a7962-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="a7962-457">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="a7962-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="a7962-458">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="a7962-458">Conventions.Add</span></span><ul><li><span data-ttu-id="a7962-459">IPageRouteModelKonwencja</span><span class="sxs-lookup"><span data-stu-id="a7962-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="a7962-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="a7962-461">IPageHandlerModelKonwencja</span><span class="sxs-lookup"><span data-stu-id="a7962-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="a7962-462">Dodawanie szablonu trasy i nagłówka do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="a7962-463">Konwencje akcji marszruty strony</span><span class="sxs-lookup"><span data-stu-id="a7962-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="a7962-464">DodajfolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="a7962-465">Strona AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="a7962-466">Dodaj stronęProsie</span><span class="sxs-lookup"><span data-stu-id="a7962-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="a7962-467">Dodawanie szablonu trasy do stron w folderze i do jednej strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="a7962-468">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="a7962-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="a7962-469">DodajfolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="a7962-470">Strona AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="a7962-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="a7962-471">ConfigureFilter (klasa filtru, wyrażenie lambda lub fabryka filtrów)</span><span class="sxs-lookup"><span data-stu-id="a7962-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="a7962-472">Dodaj nagłówek do stron w folderze, dodaj nagłówek do jednej strony i skonfiguruj [fabrykę filtrów,](xref:mvc/controllers/filters#ifilterfactory) aby dodać nagłówek do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="a7962-473">Konwencje stron maszynki do golenia <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> są <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> dodawane i konfigurowane przy użyciu metody rozszerzenia do kolekcji usług w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="a7962-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="a7962-474">Następujące przykłady konwencji są wyjaśnione w dalszej części tego tematu:</span><span class="sxs-lookup"><span data-stu-id="a7962-474">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="a7962-475">Kolejność marszruty</span><span class="sxs-lookup"><span data-stu-id="a7962-475">Route order</span></span>

<span data-ttu-id="a7962-476">Trasy określają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do przetworzenia (dopasowywanie tras).</span><span class="sxs-lookup"><span data-stu-id="a7962-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="a7962-477">Zamówienie</span><span class="sxs-lookup"><span data-stu-id="a7962-477">Order</span></span>            | <span data-ttu-id="a7962-478">Zachowanie</span><span class="sxs-lookup"><span data-stu-id="a7962-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="a7962-479">-1</span><span class="sxs-lookup"><span data-stu-id="a7962-479">-1</span></span>               | <span data-ttu-id="a7962-480">Trasa jest przetwarzana przed przetworzeniem innych tras.</span><span class="sxs-lookup"><span data-stu-id="a7962-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="a7962-481">0</span><span class="sxs-lookup"><span data-stu-id="a7962-481">0</span></span>                | <span data-ttu-id="a7962-482">Kolejność nie jest określona (wartość domyślna).</span><span class="sxs-lookup"><span data-stu-id="a7962-482">Order isn't specified (default value).</span></span> <span data-ttu-id="a7962-483">Nie `Order` przypisywanie`Order = null`( ) `Order` domyślnie trasy do 0 (zero) do przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="a7962-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="a7962-484">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="a7962-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="a7962-485">Określa kolejność przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="a7962-486">Przetwarzanie trasy jest ustanawiane zgodnie z konwencją:</span><span class="sxs-lookup"><span data-stu-id="a7962-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="a7962-487">Trasy są przetwarzane w kolejności (-1, 0, 1, &hellip; 2, n).</span><span class="sxs-lookup"><span data-stu-id="a7962-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="a7962-488">Gdy trasy mają `Order`to samo , najbardziej konkretna trasa jest dopasowywała najpierw mniej konkretne trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="a7962-489">Gdy trasy o `Order` tej samej i takiej samej liczbie parametrów odpowiadają adresowi URL <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>żądania, trasy są przetwarzane w kolejności, w jakiej są dodawane do pliku .</span><span class="sxs-lookup"><span data-stu-id="a7962-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="a7962-490">Jeśli to możliwe, należy unikać w zależności od ustalonego zamówienia przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="a7962-491">Ogólnie rzecz biorąc routing wybiera prawidłową trasę z dopasowaniem adresu URL.</span><span class="sxs-lookup"><span data-stu-id="a7962-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="a7962-492">Jeśli musisz ustawić `Order` właściwości trasy do routingu żądań poprawnie, schemat routingu aplikacji jest prawdopodobnie mylące dla klientów i kruche do utrzymania.</span><span class="sxs-lookup"><span data-stu-id="a7962-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="a7962-493">Dążyć do uproszczenia schematu routingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="a7962-494">Przykładowa aplikacja wymaga jawnego zamówienia przetwarzania trasy, aby zademonstrować kilka scenariuszy routingu przy użyciu jednej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7962-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="a7962-495">Należy jednak podjąć próbę uniknięcia praktyki `Order` ustawiania trasy w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="a7962-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="a7962-496">Routing stron razor i routing kontrolera MVC współużytkują implementację.</span><span class="sxs-lookup"><span data-stu-id="a7962-496">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="a7962-497">Informacje o kolejności marszruty w tematach MVC są dostępne w [serwisie Routing do akcji kontrolera: Zamawianie tras atrybutów](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="a7962-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="a7962-498">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="a7962-498">Model conventions</span></span>

<span data-ttu-id="a7962-499">Dodaj <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pełnomocnika, aby dodać [konwencje modelu,](xref:mvc/controllers/application-model#conventions) które mają zastosowanie do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="a7962-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="a7962-500">Dodawanie konwencji modelu trasy do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="a7962-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="a7962-501">Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu trasy strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="a7962-502">Przykładowa aplikacja `{globalTemplate?}` dodaje szablon trasy do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="a7962-503">Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `1`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a7962-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="a7962-504">Zapewnia to następujące zachowanie dopasowywania trasy w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="a7962-505">Szablon trasy `TheContactPage/{text?}` dla jest dodawany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="a7962-506">Trasa strony kontaktu ma domyślną kolejność `null` (`Order = 0`), `{globalTemplate?}` więc jest zgodna przed szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="a7962-507">Szablon `{aboutTemplate?}` trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="a7962-508">Szablon `{aboutTemplate?}` otrzymuje . `Order` `2`</span><span class="sxs-lookup"><span data-stu-id="a7962-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="a7962-509">Gdy strona Informacje jest `/About/RouteDataValue`wymagane w , "RouteDataValue" jest `RouteData.Values["aboutTemplate"]` `Order = 2`ładowany do `Order` `RouteData.Values["globalTemplate"]` (`Order = 1`) i nie ( ) z powodu ustawienia właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="a7962-510">Szablon `{otherPagesTemplate?}` trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="a7962-511">Szablon `{otherPagesTemplate?}` otrzymuje . `Order` `2`</span><span class="sxs-lookup"><span data-stu-id="a7962-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="a7962-512">Gdy dowolna strona w folderze *Pages/OtherPages* jest żądana z parametrem trasy (na `/OtherPages/Page1/RouteDataValue` `RouteData.Values["globalTemplate"]` przykład), "RouteDataValue" jest ładowany`Order = 1`do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="a7962-513">Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje.</span><span class="sxs-lookup"><span data-stu-id="a7962-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="a7962-514">Polegaj na routingu, aby wybrać właściwą trasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="a7962-515">Opcje stron maszynki do <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>golenia, takie jak dodawanie , `Startup.ConfigureServices`są dodawane po dodaniu MVC do kolekcji usługi w pliku .</span><span class="sxs-lookup"><span data-stu-id="a7962-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a7962-516">Na przykład zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="a7962-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="a7962-517">Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue` o i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Strona Informacje jest żądana z segmentem trasy GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="a7962-520">Dodawanie konwencji modelu aplikacji do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="a7962-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="a7962-521">Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas tworzenia modelu aplikacji strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="a7962-522">Aby zademonstrować tę i inne konwencje w dalszej części tematu, przykładowa aplikacja zawiera `AddHeaderAttribute` klasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="a7962-523">Konstruktor klasy akceptuje `name` zestaw `values` ciągów i tablicy ciągów.</span><span class="sxs-lookup"><span data-stu-id="a7962-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="a7962-524">Te wartości są `OnResultExecuting` używane w jego metody, aby ustawić nagłówek odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a7962-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="a7962-525">Pełna klasa jest wyświetlana w sekcji [Konwencje akcji modelu strony](#page-model-action-conventions) w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="a7962-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="a7962-526">Przykładowa aplikacja używa `AddHeaderAttribute` klasy, aby `GlobalHeader`dodać nagłówek, do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="a7962-527">*Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="a7962-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="a7962-528">Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie Informacje pokazują, że globalheader został dodany.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="a7962-530">Dodawanie konwencji modelu programu obsługi do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="a7962-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="a7962-531">Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu programu obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="a7962-532">*Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="a7962-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="a7962-533">Konwencje akcji marszruty strony</span><span class="sxs-lookup"><span data-stu-id="a7962-533">Page route action conventions</span></span>

<span data-ttu-id="a7962-534">Domyślny dostawca modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> który wywodzi się z konwencji wywoływania, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania tras stron.</span><span class="sxs-lookup"><span data-stu-id="a7962-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="a7962-535">Konwencja modelu trasy folderu</span><span class="sxs-lookup"><span data-stu-id="a7962-535">Folder route model convention</span></span>

<span data-ttu-id="a7962-536">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na wszystkich stronach w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="a7962-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="a7962-537">Przykładowa aplikacja <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> służy do `{otherPagesTemplate?}` dodawania szablonu trasy do stron w folderze *OtherPages:*</span><span class="sxs-lookup"><span data-stu-id="a7962-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="a7962-538">Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a7962-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="a7962-539">Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="a7962-540">Jeśli strona w folderze *Pages/OtherPages* jest żądana z wartością parametru trasy (na `/OtherPages/Page1/RouteDataValue`przykład), `RouteData.Values["globalTemplate"]` `Order = 1`"RouteDataValue" jest ładowany do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="a7962-541">Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje.</span><span class="sxs-lookup"><span data-stu-id="a7962-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="a7962-542">Polegaj na routingu, aby wybrać właściwą trasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="a7962-543">Poproś o przykładową stronę `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` Page1 i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Strona1 w folderze OtherPages jest żądana z segmentem trasy GlobalRouteValue i OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="a7962-546">Konwencja modelu trasy strony</span><span class="sxs-lookup"><span data-stu-id="a7962-546">Page route model convention</span></span>

<span data-ttu-id="a7962-547">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na stronie o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="a7962-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="a7962-548">Przykładowa aplikacja `AddPageRouteModelConvention` służy do `{aboutTemplate?}` dodawania szablonu trasy do strony Informacje:</span><span class="sxs-lookup"><span data-stu-id="a7962-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="a7962-549">Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a7962-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="a7962-550">Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="a7962-551">Jeśli strona Informacje jest żądana z `/About/RouteDataValue`wartością parametru trasy w , `RouteData.Values["globalTemplate"]` "RouteDataValue" jest ładowany do (`Order = 1`) i nie `RouteData.Values["aboutTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a7962-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="a7962-552">Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje.</span><span class="sxs-lookup"><span data-stu-id="a7962-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="a7962-553">Polegaj na routingu, aby wybrać właściwą trasę.</span><span class="sxs-lookup"><span data-stu-id="a7962-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="a7962-554">Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue/AboutRouteValue` o i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Strona Informacje jest żądana z segmentami trasy dla GlobalRouteValue i AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="a7962-557">Konfigurowanie trasy strony</span><span class="sxs-lookup"><span data-stu-id="a7962-557">Configure a page route</span></span>

<span data-ttu-id="a7962-558">Służy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> do konfigurowania trasy do strony w określonej ścieżce strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="a7962-559">Wygenerowane łącza do strony używają określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="a7962-560">`AddPageRoute`do `AddPageRouteModelConvention` ustalenia trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="a7962-561">Przykładowa aplikacja tworzy `/TheContactPage` trasę do *contact.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a7962-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="a7962-562">Do strony Kontakt można `/Contact` również uzyskać pod adresem za pomocą jej domyślnej trasy.</span><span class="sxs-lookup"><span data-stu-id="a7962-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="a7962-563">Niestandardowa trasa aplikacji przykładowej do strony Kontakt `text` umożliwia`{text?}`opcjonalny segment trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="a7962-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="a7962-564">Strona zawiera również ten opcjonalny segment w swojej `@page` dyrektywie `/Contact` w przypadku, gdy użytkownik uzyskuje dostęp do strony na swojej trasie:</span><span class="sxs-lookup"><span data-stu-id="a7962-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="a7962-565">Należy zauważyć, że adres URL wygenerowany dla łącza **Kontakt** na renderowanej stronie odzwierciedla zaktualizowaną trasę:</span><span class="sxs-lookup"><span data-stu-id="a7962-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Przykładowe łącze kontaktowe aplikacji na pasku nawigacyjnym](razor-pages-conventions/_static/contact-link.png)

![Sprawdzenie łącza Kontakt w renderowanym kodzie HTML wskazuje, że href jest ustawiony na "/TheContactPage"](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="a7962-568">Odwiedź stronę Kontakt przy zwykłej `/Contact`trasie lub trasie `/TheContactPage`niestandardowej .</span><span class="sxs-lookup"><span data-stu-id="a7962-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="a7962-569">Jeśli podasz `text` dodatkowy segment trasy, strona pokazuje segment zakodowany w formacie HTML, który podajesz:</span><span class="sxs-lookup"><span data-stu-id="a7962-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Przykład przeglądarki krawędzi dostarczania opcjonalnego segmentu trasy "tekst" "TextValue" w adresie URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="a7962-572">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="a7962-572">Page model action conventions</span></span>

<span data-ttu-id="a7962-573">Domyślny dostawca modelu strony, który implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> wywoływania konwencji, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania modeli stron.</span><span class="sxs-lookup"><span data-stu-id="a7962-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="a7962-574">Konwencje te są przydatne podczas tworzenia i modyfikowania scenariuszy odnajdowania i przetwarzania strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="a7962-575">W przykładach w tej sekcji przykładowa `AddHeaderAttribute` aplikacja używa klasy, która jest <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, która stosuje nagłówek odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="a7962-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="a7962-576">Korzystając z konwencji, w przykładzie pokazano, jak zastosować atrybut do wszystkich stron w folderze i do jednej strony.</span><span class="sxs-lookup"><span data-stu-id="a7962-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="a7962-577">**Konwencja modelu aplikacji folderu**</span><span class="sxs-lookup"><span data-stu-id="a7962-577">**Folder app model convention**</span></span>

<span data-ttu-id="a7962-578">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> wywołującej akcję dla wystąpień dla wszystkich stron w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="a7962-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="a7962-579">Przykład pokazuje użycie `AddFolderApplicationModelConvention` przez dodanie nagłówka, `OtherPagesHeader`do stron wewnątrz *folderu OtherPages* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="a7962-580">Poproś przykładową stronę Page1 `localhost:5000/OtherPages/Page1` i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi strony OtherPages/Page1 pokazują, że dodano otherpagesheader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="a7962-582">**Konwencja modelu aplikacji strony**</span><span class="sxs-lookup"><span data-stu-id="a7962-582">**Page app model convention**</span></span>

<span data-ttu-id="a7962-583">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> która wywołuje akcję na stronie o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="a7962-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="a7962-584">Przykład pokazuje użycie `AddPageApplicationModelConvention` przez dodanie nagłówka, `AboutHeader`do Informacje strony:</span><span class="sxs-lookup"><span data-stu-id="a7962-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="a7962-585">Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie Informacje pokazują, że aboutheader został dodany.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="a7962-587">**Konfigurowanie filtru**</span><span class="sxs-lookup"><span data-stu-id="a7962-587">**Configure a filter**</span></span>

<span data-ttu-id="a7962-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określony filtr do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="a7962-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="a7962-589">Można zaimplementować klasę filtru, ale przykładowa aplikacja pokazuje, jak zaimplementować filtr w wyrażeniu lambda, który jest implementowany za kulisami jako fabryka, która zwraca filtr:</span><span class="sxs-lookup"><span data-stu-id="a7962-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="a7962-590">Model aplikacji strony służy do sprawdzania ścieżki względnej dla segmentów prowadzących do strony Page2 w folderze *OtherPages.*</span><span class="sxs-lookup"><span data-stu-id="a7962-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="a7962-591">Jeśli warunek przejdzie, zostanie dodany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="a7962-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="a7962-592">Jeśli nie, `EmptyFilter` jest stosowany.</span><span class="sxs-lookup"><span data-stu-id="a7962-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="a7962-593">`EmptyFilter`jest [filtrem akcji](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="a7962-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="a7962-594">Ponieważ filtry akcji są ignorowane przez `EmptyFilter` strony Razor Pages, nie ma `OtherPages/Page2`wpływu zgodnie z przeznaczeniem, jeśli ścieżka nie zawiera .</span><span class="sxs-lookup"><span data-stu-id="a7962-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="a7962-595">Poproś przykładową stronę Page2 `localhost:5000/OtherPages/Page2` o pozycję Page2 i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header zostanie dodany do odpowiedzi dla Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="a7962-597">**Konfigurowanie fabryki filtrów**</span><span class="sxs-lookup"><span data-stu-id="a7962-597">**Configure a filter factory**</span></span>

<span data-ttu-id="a7962-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określoną fabrykę, aby zastosować [filtry](xref:mvc/controllers/filters) do wszystkich stron Razor.</span><span class="sxs-lookup"><span data-stu-id="a7962-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="a7962-599">Przykładowa aplikacja zawiera przykład użycia [fabryki filtrów](xref:mvc/controllers/filters#ifilterfactory) `FilterFactoryHeader`przez dodanie nagłówka, z dwiema wartościami do stron aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7962-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="a7962-600">*AddHeaderWithFactory.cs:*</span><span class="sxs-lookup"><span data-stu-id="a7962-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="a7962-601">Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:</span><span class="sxs-lookup"><span data-stu-id="a7962-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie Informacje pokazują, że dodano dwa nagłówki FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="a7962-603">Filtry MVC i filtr strony (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="a7962-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="a7962-604">[Filtry akcji](xref:mvc/controllers/filters#action-filters) MVC są ignorowane przez strony Razor Pages, ponieważ razor Pages używają metod obsługi.</span><span class="sxs-lookup"><span data-stu-id="a7962-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="a7962-605">Dostępne są inne typy filtrów MVC: [Autoryzacja,](xref:mvc/controllers/filters#authorization-filters) [Wyjątek,](xref:mvc/controllers/filters#exception-filters) [Zasób](xref:mvc/controllers/filters#resource-filters)i [Wynik](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="a7962-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="a7962-606">Aby uzyskać więcej informacji, zobacz temat [Filtry.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="a7962-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="a7962-607">Filtr Strony<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) to filtr, który ma zastosowanie do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="a7962-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="a7962-608">Aby uzyskać więcej informacji, zobacz [Metody filtrowania stron razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a7962-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7962-609">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a7962-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
