---
title: 'Razor Konwencje dotyczące tras i aplikacji na stronach ASP.NET Core'
author: rick-anderson
description: Dowiedz się, w jaki sposób Konwencja i konwencje dostawcy modelu aplikacji ułatwiają kontrolowanie routingu, odnajdywania i przetwarzania stron.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 2947bf0b697ca01f17d260b9f31aa3cc79d457b6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059874"
---
# <a name="no-locrazor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="f37a1-103">Razor Konwencje dotyczące tras i aplikacji na stronach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f37a1-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f37a1-104">Dowiedz się, w jaki sposób używać [konwencji dotyczących trasy strony i dostawcy modelu aplikacji](xref:mvc/controllers/application-model#conventions) do kontrolowania routingu, odnajdywania i przetwarzania stron w Razor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="f37a1-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="f37a1-105">Jeśli trzeba skonfigurować niestandardowe trasy stron dla poszczególnych stron, skonfiguruj Routing do stron z [Konwencją AddPageRoute](#configure-a-page-route) opisanej w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="f37a1-106">Aby określić trasę strony, dodać segmenty tras lub dodać parametry do trasy, użyj `@page` dyrektywy strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="f37a1-107">Aby uzyskać więcej informacji, zobacz [niestandardowe trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="f37a1-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="f37a1-108">Istnieją słowa zastrzeżone, których nie można używać jako segmentów tras ani nazw parametrów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="f37a1-109">Aby uzyskać więcej informacji, zobacz [Routing: zastrzeżone nazwy routingu](xref:mvc/controllers/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="f37a1-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="f37a1-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f37a1-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="f37a1-111">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="f37a1-111">Scenario</span></span> | <span data-ttu-id="f37a1-112">Przykład ilustruje...</span><span class="sxs-lookup"><span data-stu-id="f37a1-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="f37a1-113">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="f37a1-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="f37a1-114">Konwencje. Add</span><span class="sxs-lookup"><span data-stu-id="f37a1-114">Conventions.Add</span></span><ul><li><span data-ttu-id="f37a1-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="f37a1-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f37a1-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="f37a1-118">Dodaj szablon trasy i nagłówek do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="f37a1-119">Konwencje akcji trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="f37a1-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="f37a1-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="f37a1-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="f37a1-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="f37a1-123">Dodaj szablon trasy do stron w folderze i do pojedynczej strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="f37a1-124">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="f37a1-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="f37a1-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f37a1-127">ConfigureFilter (Klasa filtru, wyrażenie lambda lub fabryka filtrów)</span><span class="sxs-lookup"><span data-stu-id="f37a1-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="f37a1-128">Dodaj nagłówek do stron w folderze, Dodaj nagłówek do jednej strony i skonfiguruj [fabrykę filtrów](xref:mvc/controllers/filters#ifilterfactory) , aby dodać nagłówek do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="f37a1-129">Razor Konwencje stron są konfigurowane przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> przeciążenia, które konfiguruje <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-129">Razor Pages conventions are configured using an <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f37a1-130">Poniższe przykłady Konwencji zostały omówione w dalszej części tego tematu:</span><span class="sxs-lookup"><span data-stu-id="f37a1-130">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
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

## <a name="route-order"></a><span data-ttu-id="f37a1-131">Kolejność tras</span><span class="sxs-lookup"><span data-stu-id="f37a1-131">Route order</span></span>

<span data-ttu-id="f37a1-132">Trasy określają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do przetwarzania (dopasowanie trasy).</span><span class="sxs-lookup"><span data-stu-id="f37a1-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="f37a1-133">Zamówienie</span><span class="sxs-lookup"><span data-stu-id="f37a1-133">Order</span></span>            | <span data-ttu-id="f37a1-134">Zachowanie</span><span class="sxs-lookup"><span data-stu-id="f37a1-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="f37a1-135">-1</span><span class="sxs-lookup"><span data-stu-id="f37a1-135">-1</span></span>               | <span data-ttu-id="f37a1-136">Trasa jest przetwarzana przed przetworzeniem innych tras.</span><span class="sxs-lookup"><span data-stu-id="f37a1-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="f37a1-137">0</span><span class="sxs-lookup"><span data-stu-id="f37a1-137">0</span></span>                | <span data-ttu-id="f37a1-138">Nie określono kolejności (wartość domyślna).</span><span class="sxs-lookup"><span data-stu-id="f37a1-138">Order isn't specified (default value).</span></span> <span data-ttu-id="f37a1-139">Przypisanie `Order` ( `Order = null` ) domyślnie trasy `Order` do 0 (zero) do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="f37a1-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="f37a1-140">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="f37a1-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="f37a1-141">Określa kolejność przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="f37a1-142">Przetwarzanie trasy zostało ustanowione według Konwencji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="f37a1-143">Trasy są przetwarzane w kolejności sekwencyjnej (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="f37a1-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="f37a1-144">Gdy trasy są takie same `Order` , najpierw pasuje do najbardziej określonej trasy, a następnie mniej konkretnych tras.</span><span class="sxs-lookup"><span data-stu-id="f37a1-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="f37a1-145">Gdy trasy o tej samej `Order` i tej samej liczbie parametrów pasują do adresu URL żądania, trasy są przetwarzane w kolejności, w jakiej są dodawane do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="f37a1-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="f37a1-146">Jeśli to możliwe, unikaj w zależności od ustalonej kolejności przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="f37a1-147">Ogólnie rzecz biorąc, routing wybiera prawidłową trasę z dopasowywaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="f37a1-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="f37a1-148">Jeśli musisz ustawić właściwości trasy `Order` , aby poprawnie kierować żądania, schemat routingu aplikacji jest prawdopodobnie mylący dla klientów i jest nierozsądny do utrzymania.</span><span class="sxs-lookup"><span data-stu-id="f37a1-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="f37a1-149">Postaraj się, aby uprościć schemat routingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="f37a1-150">Przykładowa aplikacja wymaga jawnej kolejności przetwarzania trasy, aby przedstawić kilka scenariuszy routingu przy użyciu jednej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="f37a1-151">Należy jednak podjąć próbę uniknięcia praktycznego ustawienia trasy `Order` w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="f37a1-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="f37a1-152">Razor Strony routingu i routingu kontrolera MVC współdzielą implementację.</span><span class="sxs-lookup"><span data-stu-id="f37a1-152">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="f37a1-153">Informacje o zamówieniu trasy w tematach MVC są dostępne w obszarze [routing do akcji kontrolera: porządkowanie tras atrybutów](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="f37a1-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="f37a1-154">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="f37a1-154">Model conventions</span></span>

<span data-ttu-id="f37a1-155">Dodaj delegata <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> , aby dodać [konwencje modelu](xref:mvc/controllers/application-model#conventions) , które mają zastosowanie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="f37a1-156">Dodawanie Konwencji modelu trasy do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="f37a1-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="f37a1-157">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Aby utworzyć i dodać <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> wystąpień, które są stosowane podczas konstruowania modelu trasy strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="f37a1-158">Przykładowa aplikacja dodaje `{globalTemplate?}` szablon trasy do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="f37a1-159"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Właściwość dla elementu <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest ustawiona na `1` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="f37a1-160">Zapewnia to zachowanie dopasowania trasy w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="f37a1-161">Szablon trasy dla programu `TheContactPage/{text?}` został dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="f37a1-162">Trasa strony kontaktowej ma domyślną kolejność `null` ( `Order = 0` ), więc pasuje przed `{globalTemplate?}` szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="f37a1-163">`{aboutTemplate?}`Szablon trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f37a1-164">`{aboutTemplate?}`Szablon ma `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f37a1-165">Gdy strona informacje jest wymagana w lokalizacji `/About/RouteDataValue` , "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` ( `Order = 1` ), a nie `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="f37a1-166">`{otherPagesTemplate?}`Szablon trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f37a1-167">`{otherPagesTemplate?}`Szablon ma `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f37a1-168">Gdy zażądano dowolnej strony w folderze *Pages/OtherPages* z parametrem trasy (na przykład `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` ( `Order = 1` ), a nie `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f37a1-169">Wszędzie tam, gdzie to możliwe, nie ustawiaj `Order` , która powoduje `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f37a1-170">Należy polegać na routingu w celu wybrania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f37a1-171">Razor Opcje stron, takie jak dodawanie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , są dodawane po Razor dodaniu stron do kolekcji usług w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when Razor Pages is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f37a1-172">Aby zapoznać się z przykładem, zobacz przykładową [aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="f37a1-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f37a1-173">Zażądaj przykładowej strony o podanej godzinie `localhost:5000/About/GlobalRouteValue` i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="f37a1-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Strona informacje jest wymagana z segmentem trasy GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="f37a1-176">Dodawanie Konwencji modelu aplikacji do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="f37a1-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="f37a1-177">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Aby utworzyć i dodać <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> wystąpień, które są stosowane podczas konstruowania modelu aplikacji na stronie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="f37a1-178">Aby przedstawić te i inne konwencje w dalszej części tematu, przykładowa aplikacja zawiera `AddHeaderAttribute` klasę.</span><span class="sxs-lookup"><span data-stu-id="f37a1-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="f37a1-179">Konstruktor klasy akceptuje `name` ciąg i `values` tablicę ciągów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="f37a1-180">Te wartości są używane w `OnResultExecuting` metodzie do ustawiania nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f37a1-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="f37a1-181">Pełna Klasa jest wyświetlana w sekcji [konwencje akcji modelu strony](#page-model-action-conventions) w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="f37a1-182">Przykładowa aplikacja używa `AddHeaderAttribute` klasy do dodawania nagłówka, `GlobalHeader` do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="f37a1-183">*Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-183">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f37a1-184">Zażądaj strony o podanej próbce `localhost:5000/About` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie informacje pokazują, że GlobalHeader został dodany.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="f37a1-186">Dodawanie Konwencji modelu programu obsługi do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="f37a1-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="f37a1-187">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Aby utworzyć i dodać <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> wystąpień, które są stosowane podczas konstruowania modelu obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="f37a1-188">*Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-188">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="f37a1-189">Konwencje akcji trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-189">Page route action conventions</span></span>

<span data-ttu-id="f37a1-190">Domyślny dostawca modelu trasy, który pochodzi od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> Konwencji Invoke, zaprojektowanych w celu zapewnienia punktów rozszerzalności do konfigurowania tras strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="f37a1-191">Konwencja modelu trasy folderu</span><span class="sxs-lookup"><span data-stu-id="f37a1-191">Folder route model convention</span></span>

<span data-ttu-id="f37a1-192">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , który wywołuje akcję na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> wszystkich stronach w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="f37a1-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="f37a1-193">Aplikacja Przykładowa używa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do dodawania `{otherPagesTemplate?}` szablonu trasy do stron w folderze *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f37a1-194"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Właściwość dla elementu <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest ustawiona na `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f37a1-195">Dzięki temu szablon dla `{globalTemplate?}` (ustawione wcześniej w temacie do `1` ) ma priorytet dla pierwszej pozycji wartości danych trasy, gdy podano wartość pojedynczej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f37a1-196">Jeśli zażądano strony w folderze *Pages/OtherPages* z wartością parametru trasy (na przykład `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` (), `Order = 1` a nie `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f37a1-197">Wszędzie tam, gdzie to możliwe, nie ustawiaj `Order` , która powoduje `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f37a1-198">Należy polegać na routingu w celu wybrania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f37a1-199">Zażądaj strony z przykładową stroną `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="f37a1-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Żądanie Strona1 w folderze OtherPages z segmentem trasy GlobalRouteValue i OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="f37a1-202">Konwencja modelu trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-202">Page route model convention</span></span>

<span data-ttu-id="f37a1-203">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , który wywołuje akcję na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stronie dla strony o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="f37a1-204">Aplikacja Przykładowa używa `AddPageRouteModelConvention` do dodawania `{aboutTemplate?}` szablonu trasy do strony informacje:</span><span class="sxs-lookup"><span data-stu-id="f37a1-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f37a1-205"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Właściwość dla elementu <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest ustawiona na `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f37a1-206">Dzięki temu szablon dla `{globalTemplate?}` (ustawione wcześniej w temacie do `1` ) ma priorytet dla pierwszej pozycji wartości danych trasy, gdy podano wartość pojedynczej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f37a1-207">Jeśli zażądano strony o wartości parametru trasy w lokalizacji `/About/RouteDataValue` , "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` ( `Order = 1` ), a nie `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f37a1-208">Wszędzie tam, gdzie to możliwe, nie ustawiaj `Order` , która powoduje `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f37a1-209">Należy polegać na routingu w celu wybrania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f37a1-210">Zażądaj przykładowej strony o podanej godzinie `localhost:5000/About/GlobalRouteValue/AboutRouteValue` i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="f37a1-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Żądanie strony about z segmentami trasy dla GlobalRouteValue i AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="f37a1-213">Dostosowywanie tras stron przy użyciu transformatora parametrów</span><span class="sxs-lookup"><span data-stu-id="f37a1-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="f37a1-214">Trasy stron generowane przez ASP.NET Core mogą być dostosowywane przy użyciu transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="f37a1-215">Transformator parametrów implementuje `IOutboundParameterTransformer` i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="f37a1-216">Na przykład niestandardowy `SlugifyParameterTransformer` transformator parametrów zmienia `SubscriptionManagement` wartość trasy na `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="f37a1-217">`PageRouteTransformerConvention`Konwencja model trasy strony stosuje transformator parametrów do segmentów nazw folderów i plików w przypadku automatycznie generowanych tras stron w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="f37a1-218">Na przykład Razor plik Pages w lokalizacji */Pages/SubscriptionManagement/ViewAll.cshtml* będzie mógł zostać ponownie zapisany w `/SubscriptionManagement/ViewAll` usłudze do `/subscription-management/view-all` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="f37a1-219">`PageRouteTransformerConvention` przekształca automatycznie generowane segmenty trasy strony, która pochodzi z Razor folderu stron i nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="f37a1-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="f37a1-220">Nie przekształca segmentów tras dodanych do `@page` dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="f37a1-221">Konwencja nie przetwarza również tras dodanych przez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .</span><span class="sxs-lookup"><span data-stu-id="f37a1-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="f37a1-222">`PageRouteTransformerConvention`Zarejestrowano jako opcję w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f37a1-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a><span data-ttu-id="f37a1-223">Konfigurowanie trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-223">Configure a page route</span></span>

<span data-ttu-id="f37a1-224">Służy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> do konfigurowania trasy do strony pod określoną ścieżką strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="f37a1-225">Wygenerowane linki do strony używają określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="f37a1-226">`AddPageRoute` używa `AddPageRouteModelConvention` do ustanowienia trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="f37a1-227">Przykładowa aplikacja tworzy trasę do `/TheContactPage` *Contact. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="f37a1-228">Na stronie kontakt można również uzyskać dostęp do tej strony przy `/Contact` użyciu trasy domyślnej.</span><span class="sxs-lookup"><span data-stu-id="f37a1-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="f37a1-229">Niestandardowa trasa aplikacji przykładowej do strony kontaktowej umożliwia określenie opcjonalnego `text` segmentu trasy ( `{text?}` ).</span><span class="sxs-lookup"><span data-stu-id="f37a1-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="f37a1-230">Strona zawiera również ten opcjonalny segment w swojej `@page` dyrektywie w przypadku, gdy osoba odwiedzająca uzyskuje dostęp do strony w swojej `/Contact` trasie:</span><span class="sxs-lookup"><span data-stu-id="f37a1-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="f37a1-231">Należy pamiętać, że adres URL wygenerowany dla linku **kontaktowego** na renderowanej stronie odzwierciedla zaktualizowaną trasę:</span><span class="sxs-lookup"><span data-stu-id="f37a1-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Link do kontaktu z przykładową aplikacją na pasku nawigacyjnym](razor-pages-conventions/_static/contact-link.png)

![Sprawdzanie linku kontaktu w renderowanym kodzie HTML wskazuje, że odwołanie href jest ustawione na wartość "/TheContactPage"](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="f37a1-234">Odwiedź stronę kontaktową na swojej zwykłej trasie `/Contact` lub w niestandardowej trasie `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="f37a1-235">W przypadku podania dodatkowego `text` segmentu trasy na stronie jest wyświetlany segment zakodowany w formacie HTML, który jest dostarczany:</span><span class="sxs-lookup"><span data-stu-id="f37a1-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Przykładowa przeglądarka brzegowa dostarczająca opcjonalny segment trasy "text" elementu "TextValue" w adresie URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="f37a1-238">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-238">Page model action conventions</span></span>

<span data-ttu-id="f37a1-239">Domyślny dostawca modelu strony, który implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konwencje wywołujące, zaprojektowane w celu zapewnienia punktów rozszerzalności do konfigurowania modeli stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="f37a1-240">Konwencje te są przydatne podczas kompilowania i modyfikowania scenariuszy przetwarzania i odnajdywania stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="f37a1-241">W przykładach w tej sekcji Przykładowa aplikacja używa `AddHeaderAttribute` klasy, która jest <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , która ma zastosowanie do nagłówka odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="f37a1-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="f37a1-242">Przy użyciu konwencji, przykład pokazuje, jak zastosować atrybut do wszystkich stron w folderze i do pojedynczej strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="f37a1-243">**Konwencja modelu aplikacji folderu**</span><span class="sxs-lookup"><span data-stu-id="f37a1-243">**Folder app model convention**</span></span>

<span data-ttu-id="f37a1-244">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , który wywołuje akcję w <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> wystąpieniach dla wszystkich stron w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="f37a1-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="f37a1-245">Przykład ilustruje użycie `AddFolderApplicationModelConvention` przez dodanie nagłówka, `OtherPagesHeader` do stron w folderze *OtherPages* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="f37a1-246">Zażądaj przykładowej strony `localhost:5000/OtherPages/Page1` i zbadaj nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi strony OtherPages/Strona1 pokazują, że OtherPagesHeader został dodany.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="f37a1-248">**Konwencja modelu aplikacji strony**</span><span class="sxs-lookup"><span data-stu-id="f37a1-248">**Page app model convention**</span></span>

<span data-ttu-id="f37a1-249">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , który wywołuje akcję na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stronie dla strony o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="f37a1-250">Przykład ilustruje użycie `AddPageApplicationModelConvention` przez dodanie nagłówka, `AboutHeader` do strony informacje:</span><span class="sxs-lookup"><span data-stu-id="f37a1-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="f37a1-251">Zażądaj strony o podanej próbce `localhost:5000/About` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie informacje pokazują, że AboutHeader został dodany.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="f37a1-253">**Konfigurowanie filtru**</span><span class="sxs-lookup"><span data-stu-id="f37a1-253">**Configure a filter**</span></span>

<span data-ttu-id="f37a1-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguruje określony filtr, który ma zostać zastosowany.</span><span class="sxs-lookup"><span data-stu-id="f37a1-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="f37a1-255">Można zaimplementować klasę filtru, ale Przykładowa aplikacja pokazuje, jak zaimplementować filtr w wyrażeniu lambda, które jest zaimplementowane w tle jako fabryka, która zwraca filtr:</span><span class="sxs-lookup"><span data-stu-id="f37a1-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="f37a1-256">Model aplikacji strony służy do sprawdzania ścieżki względnej dla segmentów, które prowadzą do strony PAGE2 w folderze *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="f37a1-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="f37a1-257">Jeśli warunek zostanie spełniony, zostanie dodany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="f37a1-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="f37a1-258">Jeśli nie, `EmptyFilter` jest stosowane.</span><span class="sxs-lookup"><span data-stu-id="f37a1-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="f37a1-259">`EmptyFilter` jest [filtrem akcji](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="f37a1-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="f37a1-260">Ze względu na to, że filtry akcji są ignorowane przez Razor strony, nie `EmptyFilter` ma ona wpływu na zamierzone, jeśli ścieżka nie zawiera `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="f37a1-261">Zażądaj strony PAGE2 próbki na `localhost:5000/OtherPages/Page2` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header jest dodawany do odpowiedzi dla PAGE2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="f37a1-263">**Konfigurowanie fabryki filtrów**</span><span class="sxs-lookup"><span data-stu-id="f37a1-263">**Configure a filter factory**</span></span>

<span data-ttu-id="f37a1-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguruje określoną fabrykę do zastosowania [filtrów](xref:mvc/controllers/filters) do wszystkich Razor stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="f37a1-265">Przykładowa aplikacja zawiera przykładowe użycie [fabryki filtrów](xref:mvc/controllers/filters#ifilterfactory) przez dodanie nagłówka, `FilterFactoryHeader` z dwoma wartościami do stron aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="f37a1-266">*AddHeaderWithFactory.cs* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-266">*AddHeaderWithFactory.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="f37a1-267">Zażądaj strony o podanej próbce `localhost:5000/About` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie informacje pokazują, że dodano dwa nagłówki FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="f37a1-269">Filtry MVC i filtr strony (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="f37a1-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="f37a1-270">[Filtry akcji](xref:mvc/controllers/filters#action-filters) MVC są ignorowane przez Razor strony, ponieważ Razor strony używają metod obsługi.</span><span class="sxs-lookup"><span data-stu-id="f37a1-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="f37a1-271">Dostępne są inne typy filtrów MVC: [autoryzacja](xref:mvc/controllers/filters#authorization-filters), [wyjątek](xref:mvc/controllers/filters#exception-filters), [zasób](xref:mvc/controllers/filters#resource-filters)i [wynik](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="f37a1-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="f37a1-272">Aby uzyskać więcej informacji, zobacz temat [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="f37a1-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="f37a1-273">Filtr strony ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) to filtr, który ma zastosowanie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="f37a1-274">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="f37a1-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f37a1-275">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f37a1-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f37a1-276">Dowiedz się, w jaki sposób używać [konwencji dotyczących trasy strony i dostawcy modelu aplikacji](xref:mvc/controllers/application-model#conventions) do kontrolowania routingu, odnajdywania i przetwarzania stron w Razor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="f37a1-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="f37a1-277">Jeśli trzeba skonfigurować niestandardowe trasy stron dla poszczególnych stron, skonfiguruj Routing do stron z [Konwencją AddPageRoute](#configure-a-page-route) opisanej w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="f37a1-278">Aby określić trasę strony, dodać segmenty tras lub dodać parametry do trasy, użyj `@page` dyrektywy strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="f37a1-279">Aby uzyskać więcej informacji, zobacz [niestandardowe trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="f37a1-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="f37a1-280">Istnieją słowa zastrzeżone, których nie można używać jako segmentów tras ani nazw parametrów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="f37a1-281">Aby uzyskać więcej informacji, zobacz [Routing: zastrzeżone nazwy routingu](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="f37a1-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="f37a1-282">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f37a1-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="f37a1-283">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="f37a1-283">Scenario</span></span> | <span data-ttu-id="f37a1-284">Przykład ilustruje...</span><span class="sxs-lookup"><span data-stu-id="f37a1-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="f37a1-285">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="f37a1-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="f37a1-286">Konwencje. Add</span><span class="sxs-lookup"><span data-stu-id="f37a1-286">Conventions.Add</span></span><ul><li><span data-ttu-id="f37a1-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="f37a1-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f37a1-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="f37a1-290">Dodaj szablon trasy i nagłówek do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="f37a1-291">Konwencje akcji trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="f37a1-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="f37a1-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="f37a1-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="f37a1-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="f37a1-295">Dodaj szablon trasy do stron w folderze i do pojedynczej strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="f37a1-296">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="f37a1-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="f37a1-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f37a1-299">ConfigureFilter (Klasa filtru, wyrażenie lambda lub fabryka filtrów)</span><span class="sxs-lookup"><span data-stu-id="f37a1-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="f37a1-300">Dodaj nagłówek do stron w folderze, Dodaj nagłówek do jednej strony i skonfiguruj [fabrykę filtrów](xref:mvc/controllers/filters#ifilterfactory) , aby dodać nagłówek do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="f37a1-301">Razor Konwencje stron są dodawane i konfigurowane przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metody rozszerzenia do <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekcji usług w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="f37a1-302">Poniższe przykłady Konwencji zostały omówione w dalszej części tego tematu:</span><span class="sxs-lookup"><span data-stu-id="f37a1-302">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="f37a1-303">Kolejność tras</span><span class="sxs-lookup"><span data-stu-id="f37a1-303">Route order</span></span>

<span data-ttu-id="f37a1-304">Trasy określają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do przetwarzania (dopasowanie trasy).</span><span class="sxs-lookup"><span data-stu-id="f37a1-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="f37a1-305">Zamówienie</span><span class="sxs-lookup"><span data-stu-id="f37a1-305">Order</span></span>            | <span data-ttu-id="f37a1-306">Zachowanie</span><span class="sxs-lookup"><span data-stu-id="f37a1-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="f37a1-307">-1</span><span class="sxs-lookup"><span data-stu-id="f37a1-307">-1</span></span>               | <span data-ttu-id="f37a1-308">Trasa jest przetwarzana przed przetworzeniem innych tras.</span><span class="sxs-lookup"><span data-stu-id="f37a1-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="f37a1-309">0</span><span class="sxs-lookup"><span data-stu-id="f37a1-309">0</span></span>                | <span data-ttu-id="f37a1-310">Nie określono kolejności (wartość domyślna).</span><span class="sxs-lookup"><span data-stu-id="f37a1-310">Order isn't specified (default value).</span></span> <span data-ttu-id="f37a1-311">Przypisanie `Order` ( `Order = null` ) domyślnie trasy `Order` do 0 (zero) do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="f37a1-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="f37a1-312">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="f37a1-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="f37a1-313">Określa kolejność przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="f37a1-314">Przetwarzanie trasy zostało ustanowione według Konwencji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="f37a1-315">Trasy są przetwarzane w kolejności sekwencyjnej (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="f37a1-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="f37a1-316">Gdy trasy są takie same `Order` , najpierw pasuje do najbardziej określonej trasy, a następnie mniej konkretnych tras.</span><span class="sxs-lookup"><span data-stu-id="f37a1-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="f37a1-317">Gdy trasy o tej samej `Order` i tej samej liczbie parametrów pasują do adresu URL żądania, trasy są przetwarzane w kolejności, w jakiej są dodawane do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="f37a1-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="f37a1-318">Jeśli to możliwe, unikaj w zależności od ustalonej kolejności przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="f37a1-319">Ogólnie rzecz biorąc, routing wybiera prawidłową trasę z dopasowywaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="f37a1-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="f37a1-320">Jeśli musisz ustawić właściwości trasy `Order` , aby poprawnie kierować żądania, schemat routingu aplikacji jest prawdopodobnie mylący dla klientów i jest nierozsądny do utrzymania.</span><span class="sxs-lookup"><span data-stu-id="f37a1-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="f37a1-321">Postaraj się, aby uprościć schemat routingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="f37a1-322">Przykładowa aplikacja wymaga jawnej kolejności przetwarzania trasy, aby przedstawić kilka scenariuszy routingu przy użyciu jednej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="f37a1-323">Należy jednak podjąć próbę uniknięcia praktycznego ustawienia trasy `Order` w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="f37a1-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="f37a1-324">Razor Strony routingu i routingu kontrolera MVC współdzielą implementację.</span><span class="sxs-lookup"><span data-stu-id="f37a1-324">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="f37a1-325">Informacje o zamówieniu trasy w tematach MVC są dostępne w obszarze [routing do akcji kontrolera: porządkowanie tras atrybutów](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="f37a1-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="f37a1-326">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="f37a1-326">Model conventions</span></span>

<span data-ttu-id="f37a1-327">Dodaj delegata <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> , aby dodać [konwencje modelu](xref:mvc/controllers/application-model#conventions) , które mają zastosowanie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="f37a1-328">Dodawanie Konwencji modelu trasy do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="f37a1-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="f37a1-329">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Aby utworzyć i dodać <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> wystąpień, które są stosowane podczas konstruowania modelu trasy strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="f37a1-330">Przykładowa aplikacja dodaje `{globalTemplate?}` szablon trasy do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="f37a1-331"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Właściwość dla elementu <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest ustawiona na `1` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="f37a1-332">Zapewnia to zachowanie dopasowania trasy w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="f37a1-333">Szablon trasy dla programu `TheContactPage/{text?}` został dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="f37a1-334">Trasa strony kontaktowej ma domyślną kolejność `null` ( `Order = 0` ), więc pasuje przed `{globalTemplate?}` szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="f37a1-335">`{aboutTemplate?}`Szablon trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f37a1-336">`{aboutTemplate?}`Szablon ma `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f37a1-337">Gdy strona informacje jest wymagana w lokalizacji `/About/RouteDataValue` , "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` ( `Order = 1` ), a nie `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="f37a1-338">`{otherPagesTemplate?}`Szablon trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f37a1-339">`{otherPagesTemplate?}`Szablon ma `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f37a1-340">Gdy zażądano dowolnej strony w folderze *Pages/OtherPages* z parametrem trasy (na przykład `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` ( `Order = 1` ), a nie `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f37a1-341">Wszędzie tam, gdzie to możliwe, nie ustawiaj `Order` , która powoduje `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f37a1-342">Należy polegać na routingu w celu wybrania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f37a1-343">Razor Opcje stron, takie jak dodawanie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , są dodawane po dodaniu MVC do kolekcji usług w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f37a1-344">Aby zapoznać się z przykładem, zobacz przykładową [aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="f37a1-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f37a1-345">Zażądaj przykładowej strony o podanej godzinie `localhost:5000/About/GlobalRouteValue` i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="f37a1-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Strona informacje jest wymagana z segmentem trasy GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="f37a1-348">Dodawanie Konwencji modelu aplikacji do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="f37a1-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="f37a1-349">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Aby utworzyć i dodać <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> wystąpień, które są stosowane podczas konstruowania modelu aplikacji na stronie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="f37a1-350">Aby przedstawić te i inne konwencje w dalszej części tematu, przykładowa aplikacja zawiera `AddHeaderAttribute` klasę.</span><span class="sxs-lookup"><span data-stu-id="f37a1-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="f37a1-351">Konstruktor klasy akceptuje `name` ciąg i `values` tablicę ciągów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="f37a1-352">Te wartości są używane w `OnResultExecuting` metodzie do ustawiania nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f37a1-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="f37a1-353">Pełna Klasa jest wyświetlana w sekcji [konwencje akcji modelu strony](#page-model-action-conventions) w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="f37a1-354">Przykładowa aplikacja używa `AddHeaderAttribute` klasy do dodawania nagłówka, `GlobalHeader` do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="f37a1-355">*Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-355">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f37a1-356">Zażądaj strony o podanej próbce `localhost:5000/About` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie informacje pokazują, że GlobalHeader został dodany.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="f37a1-358">Dodawanie Konwencji modelu programu obsługi do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="f37a1-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="f37a1-359">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Aby utworzyć i dodać <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> wystąpień, które są stosowane podczas konstruowania modelu obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="f37a1-360">*Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-360">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="f37a1-361">Konwencje akcji trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-361">Page route action conventions</span></span>

<span data-ttu-id="f37a1-362">Domyślny dostawca modelu trasy, który pochodzi od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> Konwencji Invoke, zaprojektowanych w celu zapewnienia punktów rozszerzalności do konfigurowania tras strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="f37a1-363">Konwencja modelu trasy folderu</span><span class="sxs-lookup"><span data-stu-id="f37a1-363">Folder route model convention</span></span>

<span data-ttu-id="f37a1-364">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , który wywołuje akcję na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> wszystkich stronach w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="f37a1-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="f37a1-365">Aplikacja Przykładowa używa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do dodawania `{otherPagesTemplate?}` szablonu trasy do stron w folderze *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f37a1-366"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Właściwość dla elementu <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest ustawiona na `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f37a1-367">Dzięki temu szablon dla `{globalTemplate?}` (ustawione wcześniej w temacie do `1` ) ma priorytet dla pierwszej pozycji wartości danych trasy, gdy podano wartość pojedynczej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f37a1-368">Jeśli zażądano strony w folderze *Pages/OtherPages* z wartością parametru trasy (na przykład `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` (), `Order = 1` a nie `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f37a1-369">Wszędzie tam, gdzie to możliwe, nie ustawiaj `Order` , która powoduje `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f37a1-370">Należy polegać na routingu w celu wybrania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f37a1-371">Zażądaj strony z przykładową stroną `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="f37a1-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Żądanie Strona1 w folderze OtherPages z segmentem trasy GlobalRouteValue i OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="f37a1-374">Konwencja modelu trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-374">Page route model convention</span></span>

<span data-ttu-id="f37a1-375">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , który wywołuje akcję na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stronie dla strony o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="f37a1-376">Aplikacja Przykładowa używa `AddPageRouteModelConvention` do dodawania `{aboutTemplate?}` szablonu trasy do strony informacje:</span><span class="sxs-lookup"><span data-stu-id="f37a1-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f37a1-377"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Właściwość dla elementu <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest ustawiona na `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f37a1-378">Dzięki temu szablon dla `{globalTemplate?}` (ustawione wcześniej w temacie do `1` ) ma priorytet dla pierwszej pozycji wartości danych trasy, gdy podano wartość pojedynczej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f37a1-379">Jeśli zażądano strony o wartości parametru trasy w lokalizacji `/About/RouteDataValue` , "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` ( `Order = 1` ), a nie `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f37a1-380">Wszędzie tam, gdzie to możliwe, nie ustawiaj `Order` , która powoduje `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f37a1-381">Należy polegać na routingu w celu wybrania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f37a1-382">Zażądaj przykładowej strony o podanej godzinie `localhost:5000/About/GlobalRouteValue/AboutRouteValue` i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="f37a1-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Żądanie strony about z segmentami trasy dla GlobalRouteValue i AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="f37a1-385">Dostosowywanie tras stron przy użyciu transformatora parametrów</span><span class="sxs-lookup"><span data-stu-id="f37a1-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="f37a1-386">Trasy stron generowane przez ASP.NET Core mogą być dostosowywane przy użyciu transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="f37a1-387">Transformator parametrów implementuje `IOutboundParameterTransformer` i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="f37a1-388">Na przykład niestandardowy `SlugifyParameterTransformer` transformator parametrów zmienia `SubscriptionManagement` wartość trasy na `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="f37a1-389">`PageRouteTransformerConvention`Konwencja model trasy strony stosuje transformator parametrów do segmentów nazw folderów i plików w przypadku automatycznie generowanych tras stron w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="f37a1-390">Na przykład Razor plik Pages w lokalizacji */Pages/SubscriptionManagement/ViewAll.cshtml* będzie mógł zostać ponownie zapisany w `/SubscriptionManagement/ViewAll` usłudze do `/subscription-management/view-all` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="f37a1-391">`PageRouteTransformerConvention` przekształca automatycznie generowane segmenty trasy strony, która pochodzi z Razor folderu stron i nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="f37a1-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="f37a1-392">Nie przekształca segmentów tras dodanych do `@page` dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="f37a1-393">Konwencja nie przetwarza również tras dodanych przez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .</span><span class="sxs-lookup"><span data-stu-id="f37a1-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="f37a1-394">`PageRouteTransformerConvention`Zarejestrowano jako opcję w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f37a1-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="f37a1-395">Konfigurowanie trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-395">Configure a page route</span></span>

<span data-ttu-id="f37a1-396">Służy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> do konfigurowania trasy do strony pod określoną ścieżką strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="f37a1-397">Wygenerowane linki do strony używają określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="f37a1-398">`AddPageRoute` używa `AddPageRouteModelConvention` do ustanowienia trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="f37a1-399">Przykładowa aplikacja tworzy trasę do `/TheContactPage` *Contact. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="f37a1-400">Na stronie kontakt można również uzyskać dostęp do tej strony przy `/Contact` użyciu trasy domyślnej.</span><span class="sxs-lookup"><span data-stu-id="f37a1-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="f37a1-401">Niestandardowa trasa aplikacji przykładowej do strony kontaktowej umożliwia określenie opcjonalnego `text` segmentu trasy ( `{text?}` ).</span><span class="sxs-lookup"><span data-stu-id="f37a1-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="f37a1-402">Strona zawiera również ten opcjonalny segment w swojej `@page` dyrektywie w przypadku, gdy osoba odwiedzająca uzyskuje dostęp do strony w swojej `/Contact` trasie:</span><span class="sxs-lookup"><span data-stu-id="f37a1-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="f37a1-403">Należy pamiętać, że adres URL wygenerowany dla linku **kontaktowego** na renderowanej stronie odzwierciedla zaktualizowaną trasę:</span><span class="sxs-lookup"><span data-stu-id="f37a1-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Link do kontaktu z przykładową aplikacją na pasku nawigacyjnym](razor-pages-conventions/_static/contact-link.png)

![Sprawdzanie linku kontaktu w renderowanym kodzie HTML wskazuje, że odwołanie href jest ustawione na wartość "/TheContactPage"](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="f37a1-406">Odwiedź stronę kontaktową na swojej zwykłej trasie `/Contact` lub w niestandardowej trasie `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="f37a1-407">W przypadku podania dodatkowego `text` segmentu trasy na stronie jest wyświetlany segment zakodowany w formacie HTML, który jest dostarczany:</span><span class="sxs-lookup"><span data-stu-id="f37a1-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Przykładowa przeglądarka brzegowa dostarczająca opcjonalny segment trasy "text" elementu "TextValue" w adresie URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="f37a1-410">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-410">Page model action conventions</span></span>

<span data-ttu-id="f37a1-411">Domyślny dostawca modelu strony, który implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konwencje wywołujące, zaprojektowane w celu zapewnienia punktów rozszerzalności do konfigurowania modeli stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="f37a1-412">Konwencje te są przydatne podczas kompilowania i modyfikowania scenariuszy przetwarzania i odnajdywania stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="f37a1-413">W przykładach w tej sekcji Przykładowa aplikacja używa `AddHeaderAttribute` klasy, która jest <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , która ma zastosowanie do nagłówka odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="f37a1-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="f37a1-414">Przy użyciu konwencji, przykład pokazuje, jak zastosować atrybut do wszystkich stron w folderze i do pojedynczej strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="f37a1-415">**Konwencja modelu aplikacji folderu**</span><span class="sxs-lookup"><span data-stu-id="f37a1-415">**Folder app model convention**</span></span>

<span data-ttu-id="f37a1-416">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , który wywołuje akcję w <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> wystąpieniach dla wszystkich stron w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="f37a1-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="f37a1-417">Przykład ilustruje użycie `AddFolderApplicationModelConvention` przez dodanie nagłówka, `OtherPagesHeader` do stron w folderze *OtherPages* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="f37a1-418">Zażądaj przykładowej strony `localhost:5000/OtherPages/Page1` i zbadaj nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi strony OtherPages/Strona1 pokazują, że OtherPagesHeader został dodany.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="f37a1-420">**Konwencja modelu aplikacji strony**</span><span class="sxs-lookup"><span data-stu-id="f37a1-420">**Page app model convention**</span></span>

<span data-ttu-id="f37a1-421">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , który wywołuje akcję na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stronie dla strony o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="f37a1-422">Przykład ilustruje użycie `AddPageApplicationModelConvention` przez dodanie nagłówka, `AboutHeader` do strony informacje:</span><span class="sxs-lookup"><span data-stu-id="f37a1-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="f37a1-423">Zażądaj strony o podanej próbce `localhost:5000/About` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie informacje pokazują, że AboutHeader został dodany.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="f37a1-425">**Konfigurowanie filtru**</span><span class="sxs-lookup"><span data-stu-id="f37a1-425">**Configure a filter**</span></span>

<span data-ttu-id="f37a1-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguruje określony filtr, który ma zostać zastosowany.</span><span class="sxs-lookup"><span data-stu-id="f37a1-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="f37a1-427">Można zaimplementować klasę filtru, ale Przykładowa aplikacja pokazuje, jak zaimplementować filtr w wyrażeniu lambda, które jest zaimplementowane w tle jako fabryka, która zwraca filtr:</span><span class="sxs-lookup"><span data-stu-id="f37a1-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="f37a1-428">Model aplikacji strony służy do sprawdzania ścieżki względnej dla segmentów, które prowadzą do strony PAGE2 w folderze *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="f37a1-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="f37a1-429">Jeśli warunek zostanie spełniony, zostanie dodany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="f37a1-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="f37a1-430">Jeśli nie, `EmptyFilter` jest stosowane.</span><span class="sxs-lookup"><span data-stu-id="f37a1-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="f37a1-431">`EmptyFilter` jest [filtrem akcji](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="f37a1-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="f37a1-432">Ze względu na to, że filtry akcji są ignorowane przez Razor strony, nie `EmptyFilter` ma ona wpływu na zamierzone, jeśli ścieżka nie zawiera `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="f37a1-433">Zażądaj strony PAGE2 próbki na `localhost:5000/OtherPages/Page2` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header jest dodawany do odpowiedzi dla PAGE2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="f37a1-435">**Konfigurowanie fabryki filtrów**</span><span class="sxs-lookup"><span data-stu-id="f37a1-435">**Configure a filter factory**</span></span>

<span data-ttu-id="f37a1-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguruje określoną fabrykę do zastosowania [filtrów](xref:mvc/controllers/filters) do wszystkich Razor stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="f37a1-437">Przykładowa aplikacja zawiera przykładowe użycie [fabryki filtrów](xref:mvc/controllers/filters#ifilterfactory) przez dodanie nagłówka, `FilterFactoryHeader` z dwoma wartościami do stron aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="f37a1-438">*AddHeaderWithFactory.cs* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-438">*AddHeaderWithFactory.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="f37a1-439">Zażądaj strony o podanej próbce `localhost:5000/About` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie informacje pokazują, że dodano dwa nagłówki FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="f37a1-441">Filtry MVC i filtr strony (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="f37a1-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="f37a1-442">[Filtry akcji](xref:mvc/controllers/filters#action-filters) MVC są ignorowane przez Razor strony, ponieważ Razor strony używają metod obsługi.</span><span class="sxs-lookup"><span data-stu-id="f37a1-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="f37a1-443">Dostępne są inne typy filtrów MVC: [autoryzacja](xref:mvc/controllers/filters#authorization-filters), [wyjątek](xref:mvc/controllers/filters#exception-filters), [zasób](xref:mvc/controllers/filters#resource-filters)i [wynik](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="f37a1-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="f37a1-444">Aby uzyskać więcej informacji, zobacz temat [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="f37a1-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="f37a1-445">Filtr strony ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) to filtr, który ma zastosowanie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="f37a1-446">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="f37a1-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f37a1-447">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f37a1-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f37a1-448">Dowiedz się, w jaki sposób używać [konwencji dotyczących trasy strony i dostawcy modelu aplikacji](xref:mvc/controllers/application-model#conventions) do kontrolowania routingu, odnajdywania i przetwarzania stron w Razor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="f37a1-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="f37a1-449">Jeśli trzeba skonfigurować niestandardowe trasy stron dla poszczególnych stron, skonfiguruj Routing do stron z [Konwencją AddPageRoute](#configure-a-page-route) opisanej w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="f37a1-450">Aby określić trasę strony, dodać segmenty tras lub dodać parametry do trasy, użyj `@page` dyrektywy strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="f37a1-451">Aby uzyskać więcej informacji, zobacz [niestandardowe trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="f37a1-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="f37a1-452">Istnieją słowa zastrzeżone, których nie można używać jako segmentów tras ani nazw parametrów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="f37a1-453">Aby uzyskać więcej informacji, zobacz [Routing: zastrzeżone nazwy routingu](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="f37a1-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="f37a1-454">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f37a1-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="f37a1-455">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="f37a1-455">Scenario</span></span> | <span data-ttu-id="f37a1-456">Przykład ilustruje...</span><span class="sxs-lookup"><span data-stu-id="f37a1-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="f37a1-457">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="f37a1-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="f37a1-458">Konwencje. Add</span><span class="sxs-lookup"><span data-stu-id="f37a1-458">Conventions.Add</span></span><ul><li><span data-ttu-id="f37a1-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="f37a1-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f37a1-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="f37a1-462">Dodaj szablon trasy i nagłówek do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="f37a1-463">Konwencje akcji trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="f37a1-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="f37a1-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="f37a1-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="f37a1-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="f37a1-467">Dodaj szablon trasy do stron w folderze i do pojedynczej strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="f37a1-468">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="f37a1-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="f37a1-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f37a1-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f37a1-471">ConfigureFilter (Klasa filtru, wyrażenie lambda lub fabryka filtrów)</span><span class="sxs-lookup"><span data-stu-id="f37a1-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="f37a1-472">Dodaj nagłówek do stron w folderze, Dodaj nagłówek do jednej strony i skonfiguruj [fabrykę filtrów](xref:mvc/controllers/filters#ifilterfactory) , aby dodać nagłówek do stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="f37a1-473">Razor Konwencje stron są dodawane i konfigurowane przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metody rozszerzenia do <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekcji usług w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="f37a1-474">Poniższe przykłady Konwencji zostały omówione w dalszej części tego tematu:</span><span class="sxs-lookup"><span data-stu-id="f37a1-474">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="f37a1-475">Kolejność tras</span><span class="sxs-lookup"><span data-stu-id="f37a1-475">Route order</span></span>

<span data-ttu-id="f37a1-476">Trasy określają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do przetwarzania (dopasowanie trasy).</span><span class="sxs-lookup"><span data-stu-id="f37a1-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="f37a1-477">Zamówienie</span><span class="sxs-lookup"><span data-stu-id="f37a1-477">Order</span></span>            | <span data-ttu-id="f37a1-478">Zachowanie</span><span class="sxs-lookup"><span data-stu-id="f37a1-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="f37a1-479">-1</span><span class="sxs-lookup"><span data-stu-id="f37a1-479">-1</span></span>               | <span data-ttu-id="f37a1-480">Trasa jest przetwarzana przed przetworzeniem innych tras.</span><span class="sxs-lookup"><span data-stu-id="f37a1-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="f37a1-481">0</span><span class="sxs-lookup"><span data-stu-id="f37a1-481">0</span></span>                | <span data-ttu-id="f37a1-482">Nie określono kolejności (wartość domyślna).</span><span class="sxs-lookup"><span data-stu-id="f37a1-482">Order isn't specified (default value).</span></span> <span data-ttu-id="f37a1-483">Przypisanie `Order` ( `Order = null` ) domyślnie trasy `Order` do 0 (zero) do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="f37a1-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="f37a1-484">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="f37a1-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="f37a1-485">Określa kolejność przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="f37a1-486">Przetwarzanie trasy zostało ustanowione według Konwencji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="f37a1-487">Trasy są przetwarzane w kolejności sekwencyjnej (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="f37a1-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="f37a1-488">Gdy trasy są takie same `Order` , najpierw pasuje do najbardziej określonej trasy, a następnie mniej konkretnych tras.</span><span class="sxs-lookup"><span data-stu-id="f37a1-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="f37a1-489">Gdy trasy o tej samej `Order` i tej samej liczbie parametrów pasują do adresu URL żądania, trasy są przetwarzane w kolejności, w jakiej są dodawane do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="f37a1-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="f37a1-490">Jeśli to możliwe, unikaj w zależności od ustalonej kolejności przetwarzania trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="f37a1-491">Ogólnie rzecz biorąc, routing wybiera prawidłową trasę z dopasowywaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="f37a1-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="f37a1-492">Jeśli musisz ustawić właściwości trasy `Order` , aby poprawnie kierować żądania, schemat routingu aplikacji jest prawdopodobnie mylący dla klientów i jest nierozsądny do utrzymania.</span><span class="sxs-lookup"><span data-stu-id="f37a1-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="f37a1-493">Postaraj się, aby uprościć schemat routingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="f37a1-494">Przykładowa aplikacja wymaga jawnej kolejności przetwarzania trasy, aby przedstawić kilka scenariuszy routingu przy użyciu jednej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f37a1-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="f37a1-495">Należy jednak podjąć próbę uniknięcia praktycznego ustawienia trasy `Order` w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="f37a1-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="f37a1-496">Razor Strony routingu i routingu kontrolera MVC współdzielą implementację.</span><span class="sxs-lookup"><span data-stu-id="f37a1-496">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="f37a1-497">Informacje o zamówieniu trasy w tematach MVC są dostępne w obszarze [routing do akcji kontrolera: porządkowanie tras atrybutów](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="f37a1-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="f37a1-498">Konwencje modelu</span><span class="sxs-lookup"><span data-stu-id="f37a1-498">Model conventions</span></span>

<span data-ttu-id="f37a1-499">Dodaj delegata <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> , aby dodać [konwencje modelu](xref:mvc/controllers/application-model#conventions) , które mają zastosowanie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="f37a1-500">Dodawanie Konwencji modelu trasy do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="f37a1-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="f37a1-501">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Aby utworzyć i dodać <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> wystąpień, które są stosowane podczas konstruowania modelu trasy strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="f37a1-502">Przykładowa aplikacja dodaje `{globalTemplate?}` szablon trasy do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="f37a1-503"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Właściwość dla elementu <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest ustawiona na `1` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="f37a1-504">Zapewnia to zachowanie dopasowania trasy w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="f37a1-505">Szablon trasy dla programu `TheContactPage/{text?}` został dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="f37a1-506">Trasa strony kontaktowej ma domyślną kolejność `null` ( `Order = 0` ), więc pasuje przed `{globalTemplate?}` szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="f37a1-507">`{aboutTemplate?}`Szablon trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f37a1-508">`{aboutTemplate?}`Szablon ma `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f37a1-509">Gdy strona informacje jest wymagana w lokalizacji `/About/RouteDataValue` , "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` ( `Order = 1` ), a nie `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="f37a1-510">`{otherPagesTemplate?}`Szablon trasy zostanie dodany w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f37a1-511">`{otherPagesTemplate?}`Szablon ma `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f37a1-512">Gdy zażądano dowolnej strony w folderze *Pages/OtherPages* z parametrem trasy (na przykład `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` ( `Order = 1` ), a nie `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f37a1-513">Wszędzie tam, gdzie to możliwe, nie ustawiaj `Order` , która powoduje `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f37a1-514">Należy polegać na routingu w celu wybrania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f37a1-515">Razor Opcje stron, takie jak dodawanie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , są dodawane po dodaniu MVC do kolekcji usług w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f37a1-516">Aby zapoznać się z przykładem, zobacz przykładową [aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="f37a1-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f37a1-517">Zażądaj przykładowej strony o podanej godzinie `localhost:5000/About/GlobalRouteValue` i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="f37a1-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Strona informacje jest wymagana z segmentem trasy GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="f37a1-520">Dodawanie Konwencji modelu aplikacji do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="f37a1-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="f37a1-521">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Aby utworzyć i dodać <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> wystąpień, które są stosowane podczas konstruowania modelu aplikacji na stronie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="f37a1-522">Aby przedstawić te i inne konwencje w dalszej części tematu, przykładowa aplikacja zawiera `AddHeaderAttribute` klasę.</span><span class="sxs-lookup"><span data-stu-id="f37a1-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="f37a1-523">Konstruktor klasy akceptuje `name` ciąg i `values` tablicę ciągów.</span><span class="sxs-lookup"><span data-stu-id="f37a1-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="f37a1-524">Te wartości są używane w `OnResultExecuting` metodzie do ustawiania nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f37a1-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="f37a1-525">Pełna Klasa jest wyświetlana w sekcji [konwencje akcji modelu strony](#page-model-action-conventions) w dalszej części tematu.</span><span class="sxs-lookup"><span data-stu-id="f37a1-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="f37a1-526">Przykładowa aplikacja używa `AddHeaderAttribute` klasy do dodawania nagłówka, `GlobalHeader` do wszystkich stron w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="f37a1-527">*Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-527">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f37a1-528">Zażądaj strony o podanej próbce `localhost:5000/About` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie informacje pokazują, że GlobalHeader został dodany.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="f37a1-530">Dodawanie Konwencji modelu programu obsługi do wszystkich stron</span><span class="sxs-lookup"><span data-stu-id="f37a1-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="f37a1-531">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Aby utworzyć i dodać <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> wystąpień, które są stosowane podczas konstruowania modelu obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="f37a1-532">*Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-532">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="f37a1-533">Konwencje akcji trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-533">Page route action conventions</span></span>

<span data-ttu-id="f37a1-534">Domyślny dostawca modelu trasy, który pochodzi od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> Konwencji Invoke, zaprojektowanych w celu zapewnienia punktów rozszerzalności do konfigurowania tras strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="f37a1-535">Konwencja modelu trasy folderu</span><span class="sxs-lookup"><span data-stu-id="f37a1-535">Folder route model convention</span></span>

<span data-ttu-id="f37a1-536">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , który wywołuje akcję na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> wszystkich stronach w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="f37a1-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="f37a1-537">Aplikacja Przykładowa używa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do dodawania `{otherPagesTemplate?}` szablonu trasy do stron w folderze *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f37a1-538"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Właściwość dla elementu <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest ustawiona na `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f37a1-539">Dzięki temu szablon dla `{globalTemplate?}` (ustawione wcześniej w temacie do `1` ) ma priorytet dla pierwszej pozycji wartości danych trasy, gdy podano wartość pojedynczej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f37a1-540">Jeśli zażądano strony w folderze *Pages/OtherPages* z wartością parametru trasy (na przykład `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` (), `Order = 1` a nie `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f37a1-541">Wszędzie tam, gdzie to możliwe, nie ustawiaj `Order` , która powoduje `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f37a1-542">Należy polegać na routingu w celu wybrania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f37a1-543">Zażądaj strony z przykładową stroną `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="f37a1-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Żądanie Strona1 w folderze OtherPages z segmentem trasy GlobalRouteValue i OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="f37a1-546">Konwencja modelu trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-546">Page route model convention</span></span>

<span data-ttu-id="f37a1-547">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , który wywołuje akcję na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stronie dla strony o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="f37a1-548">Aplikacja Przykładowa używa `AddPageRouteModelConvention` do dodawania `{aboutTemplate?}` szablonu trasy do strony informacje:</span><span class="sxs-lookup"><span data-stu-id="f37a1-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f37a1-549"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Właściwość dla elementu <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest ustawiona na `2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f37a1-550">Dzięki temu szablon dla `{globalTemplate?}` (ustawione wcześniej w temacie do `1` ) ma priorytet dla pierwszej pozycji wartości danych trasy, gdy podano wartość pojedynczej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f37a1-551">Jeśli zażądano strony o wartości parametru trasy w lokalizacji `/About/RouteDataValue` , "RouteDataValue" jest ładowany do `RouteData.Values["globalTemplate"]` ( `Order = 1` ), a nie `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z powodu ustawienia `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f37a1-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f37a1-552">Wszędzie tam, gdzie to możliwe, nie ustawiaj `Order` , która powoduje `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f37a1-553">Należy polegać na routingu w celu wybrania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f37a1-554">Zażądaj przykładowej strony o podanej godzinie `localhost:5000/About/GlobalRouteValue/AboutRouteValue` i sprawdź wynik:</span><span class="sxs-lookup"><span data-stu-id="f37a1-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Żądanie strony about z segmentami trasy dla GlobalRouteValue i AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="f37a1-557">Konfigurowanie trasy strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-557">Configure a page route</span></span>

<span data-ttu-id="f37a1-558">Służy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> do konfigurowania trasy do strony pod określoną ścieżką strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="f37a1-559">Wygenerowane linki do strony używają określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="f37a1-560">`AddPageRoute` używa `AddPageRouteModelConvention` do ustanowienia trasy.</span><span class="sxs-lookup"><span data-stu-id="f37a1-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="f37a1-561">Przykładowa aplikacja tworzy trasę do `/TheContactPage` *Contact. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="f37a1-562">Na stronie kontakt można również uzyskać dostęp do tej strony przy `/Contact` użyciu trasy domyślnej.</span><span class="sxs-lookup"><span data-stu-id="f37a1-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="f37a1-563">Niestandardowa trasa aplikacji przykładowej do strony kontaktowej umożliwia określenie opcjonalnego `text` segmentu trasy ( `{text?}` ).</span><span class="sxs-lookup"><span data-stu-id="f37a1-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="f37a1-564">Strona zawiera również ten opcjonalny segment w swojej `@page` dyrektywie w przypadku, gdy osoba odwiedzająca uzyskuje dostęp do strony w swojej `/Contact` trasie:</span><span class="sxs-lookup"><span data-stu-id="f37a1-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="f37a1-565">Należy pamiętać, że adres URL wygenerowany dla linku **kontaktowego** na renderowanej stronie odzwierciedla zaktualizowaną trasę:</span><span class="sxs-lookup"><span data-stu-id="f37a1-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Link do kontaktu z przykładową aplikacją na pasku nawigacyjnym](razor-pages-conventions/_static/contact-link.png)

![Sprawdzanie linku kontaktu w renderowanym kodzie HTML wskazuje, że odwołanie href jest ustawione na wartość "/TheContactPage"](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="f37a1-568">Odwiedź stronę kontaktową na swojej zwykłej trasie `/Contact` lub w niestandardowej trasie `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="f37a1-569">W przypadku podania dodatkowego `text` segmentu trasy na stronie jest wyświetlany segment zakodowany w formacie HTML, który jest dostarczany:</span><span class="sxs-lookup"><span data-stu-id="f37a1-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Przykładowa przeglądarka brzegowa dostarczająca opcjonalny segment trasy "text" elementu "TextValue" w adresie URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="f37a1-572">Konwencje akcji modelu strony</span><span class="sxs-lookup"><span data-stu-id="f37a1-572">Page model action conventions</span></span>

<span data-ttu-id="f37a1-573">Domyślny dostawca modelu strony, który implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konwencje wywołujące, zaprojektowane w celu zapewnienia punktów rozszerzalności do konfigurowania modeli stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="f37a1-574">Konwencje te są przydatne podczas kompilowania i modyfikowania scenariuszy przetwarzania i odnajdywania stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="f37a1-575">W przykładach w tej sekcji Przykładowa aplikacja używa `AddHeaderAttribute` klasy, która jest <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , która ma zastosowanie do nagłówka odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="f37a1-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="f37a1-576">Przy użyciu konwencji, przykład pokazuje, jak zastosować atrybut do wszystkich stron w folderze i do pojedynczej strony.</span><span class="sxs-lookup"><span data-stu-id="f37a1-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="f37a1-577">**Konwencja modelu aplikacji folderu**</span><span class="sxs-lookup"><span data-stu-id="f37a1-577">**Folder app model convention**</span></span>

<span data-ttu-id="f37a1-578">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , który wywołuje akcję w <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> wystąpieniach dla wszystkich stron w określonym folderze.</span><span class="sxs-lookup"><span data-stu-id="f37a1-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="f37a1-579">Przykład ilustruje użycie `AddFolderApplicationModelConvention` przez dodanie nagłówka, `OtherPagesHeader` do stron w folderze *OtherPages* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="f37a1-580">Zażądaj przykładowej strony `localhost:5000/OtherPages/Page1` i zbadaj nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi strony OtherPages/Strona1 pokazują, że OtherPagesHeader został dodany.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="f37a1-582">**Konwencja modelu aplikacji strony**</span><span class="sxs-lookup"><span data-stu-id="f37a1-582">**Page app model convention**</span></span>

<span data-ttu-id="f37a1-583">Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> do tworzenia i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , który wywołuje akcję na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stronie dla strony o określonej nazwie.</span><span class="sxs-lookup"><span data-stu-id="f37a1-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="f37a1-584">Przykład ilustruje użycie `AddPageApplicationModelConvention` przez dodanie nagłówka, `AboutHeader` do strony informacje:</span><span class="sxs-lookup"><span data-stu-id="f37a1-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="f37a1-585">Zażądaj strony o podanej próbce `localhost:5000/About` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie informacje pokazują, że AboutHeader został dodany.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="f37a1-587">**Konfigurowanie filtru**</span><span class="sxs-lookup"><span data-stu-id="f37a1-587">**Configure a filter**</span></span>

<span data-ttu-id="f37a1-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguruje określony filtr, który ma zostać zastosowany.</span><span class="sxs-lookup"><span data-stu-id="f37a1-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="f37a1-589">Można zaimplementować klasę filtru, ale Przykładowa aplikacja pokazuje, jak zaimplementować filtr w wyrażeniu lambda, które jest zaimplementowane w tle jako fabryka, która zwraca filtr:</span><span class="sxs-lookup"><span data-stu-id="f37a1-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="f37a1-590">Model aplikacji strony służy do sprawdzania ścieżki względnej dla segmentów, które prowadzą do strony PAGE2 w folderze *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="f37a1-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="f37a1-591">Jeśli warunek zostanie spełniony, zostanie dodany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="f37a1-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="f37a1-592">Jeśli nie, `EmptyFilter` jest stosowane.</span><span class="sxs-lookup"><span data-stu-id="f37a1-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="f37a1-593">`EmptyFilter` jest [filtrem akcji](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="f37a1-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="f37a1-594">Ze względu na to, że filtry akcji są ignorowane przez Razor strony, nie `EmptyFilter` ma ona wpływu na zamierzone, jeśli ścieżka nie zawiera `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="f37a1-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="f37a1-595">Zażądaj strony PAGE2 próbki na `localhost:5000/OtherPages/Page2` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header jest dodawany do odpowiedzi dla PAGE2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="f37a1-597">**Konfigurowanie fabryki filtrów**</span><span class="sxs-lookup"><span data-stu-id="f37a1-597">**Configure a filter factory**</span></span>

<span data-ttu-id="f37a1-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguruje określoną fabrykę do zastosowania [filtrów](xref:mvc/controllers/filters) do wszystkich Razor stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="f37a1-599">Przykładowa aplikacja zawiera przykładowe użycie [fabryki filtrów](xref:mvc/controllers/filters#ifilterfactory) przez dodanie nagłówka, `FilterFactoryHeader` z dwoma wartościami do stron aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f37a1-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="f37a1-600">*AddHeaderWithFactory.cs* :</span><span class="sxs-lookup"><span data-stu-id="f37a1-600">*AddHeaderWithFactory.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="f37a1-601">Zażądaj strony o podanej próbce `localhost:5000/About` i sprawdź nagłówki, aby wyświetlić wyniki:</span><span class="sxs-lookup"><span data-stu-id="f37a1-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Nagłówki odpowiedzi na stronie informacje pokazują, że dodano dwa nagłówki FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="f37a1-603">Filtry MVC i filtr strony (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="f37a1-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="f37a1-604">[Filtry akcji](xref:mvc/controllers/filters#action-filters) MVC są ignorowane przez Razor strony, ponieważ Razor strony używają metod obsługi.</span><span class="sxs-lookup"><span data-stu-id="f37a1-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="f37a1-605">Dostępne są inne typy filtrów MVC: [autoryzacja](xref:mvc/controllers/filters#authorization-filters), [wyjątek](xref:mvc/controllers/filters#exception-filters), [zasób](xref:mvc/controllers/filters#resource-filters)i [wynik](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="f37a1-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="f37a1-606">Aby uzyskać więcej informacji, zobacz temat [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="f37a1-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="f37a1-607">Filtr strony ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) to filtr, który ma zastosowanie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="f37a1-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="f37a1-608">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="f37a1-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f37a1-609">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f37a1-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
