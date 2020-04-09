---
title: Routing do akcji kontrolera w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core MVC używa oprogramowania pośredniczącego routingu do dopasowywania adresów URL przychodzących żądań i mapowania ich na akcje.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: c63313ec060c5be368fcbd20edf5f0d557046d2e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977223"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="71eb3-103">Routing do akcji kontrolera w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71eb3-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="71eb3-104">Przez [Ryan Nowak](https://github.com/rynowak), Kirk [Larkin](https://twitter.com/serpent5)i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71eb3-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71eb3-105">ASP.NET kontrolery Core używają [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) Routing do dopasowania adresów URL przychodzących żądań i mapowania ich na [akcje.](#action)</span><span class="sxs-lookup"><span data-stu-id="71eb3-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="71eb3-106">Szablony tras:</span><span class="sxs-lookup"><span data-stu-id="71eb3-106">Routes templates:</span></span>

* <span data-ttu-id="71eb3-107">Są zdefiniowane w kodzie startowym lub atrybutach.</span><span class="sxs-lookup"><span data-stu-id="71eb3-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="71eb3-108">Opisz, jak ścieżki adresów URL są dopasowywać do [akcji](#action).</span><span class="sxs-lookup"><span data-stu-id="71eb3-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="71eb3-109">Są używane do generowania adresów URL dla łączy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="71eb3-110">Wygenerowane łącza są zazwyczaj zwracane w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="71eb3-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="71eb3-111">Akcje są [kierowane konwencjonalnie](#cr) lub [kierowane atrybutem](#ar).</span><span class="sxs-lookup"><span data-stu-id="71eb3-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="71eb3-112">Umieszczenie trasy na kontrolerze lub [akcji](#action) sprawia, że jest on atrybucowy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="71eb3-113">Zobacz [Routing mieszany, aby](#routing-mixed-ref-label) uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="71eb3-114">Ten dokument:</span><span class="sxs-lookup"><span data-stu-id="71eb3-114">This document:</span></span>

* <span data-ttu-id="71eb3-115">W tym artykule wyjaśniono interakcje między MVC a routingiem:</span><span class="sxs-lookup"><span data-stu-id="71eb3-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="71eb3-116">Jak typowe aplikacje MVC korzystają z funkcji routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="71eb3-117">Obejmuje oba:</span><span class="sxs-lookup"><span data-stu-id="71eb3-117">Covers both:</span></span>
    * <span data-ttu-id="71eb3-118">[Konwencjonalnie routingu](#cr) zwykle używane z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="71eb3-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="71eb3-119">*Routing atrybutów* używany z interfejsami API REST.</span><span class="sxs-lookup"><span data-stu-id="71eb3-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="71eb3-120">Jeśli jesteś zainteresowany przede wszystkim routingu dla interfejsów API REST, przejdź do [routingu atrybut dla rest interfejsów API](#ar) sekcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="71eb3-121">Zobacz [Routing, aby](xref:fundamentals/routing) uzyskać szczegółowe informacje o routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="71eb3-122">Odnosi się do domyślnego systemu routingu dodanego w ASP.NET Core 3.0, nazywane routingiem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="71eb3-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="71eb3-123">W celu zapewnienia zgodności można używać kontrolerów z poprzednią wersją routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="71eb3-124">Instrukcje można znaleźć w [przewodniku po migracji 2.2-3.0.](xref:migration/22-to-30)</span><span class="sxs-lookup"><span data-stu-id="71eb3-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="71eb3-125">Informacje na temat materiałów referencyjnych w starszym systemie routingu można znaleźć w [wersji 2.2 tego dokumentu.](xref:mvc/controllers/routing?view=aspnetcore-2.2)</span><span class="sxs-lookup"><span data-stu-id="71eb3-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="71eb3-126">Konfigurowanie trasy konwencjonalnej</span><span class="sxs-lookup"><span data-stu-id="71eb3-126">Set up conventional route</span></span>

<span data-ttu-id="71eb3-127">`Startup.Configure`zazwyczaj ma kod podobny do następującego podczas korzystania z [konwencjonalnego routingu:](#crd)</span><span class="sxs-lookup"><span data-stu-id="71eb3-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="71eb3-128">Wewnątrz wywołania <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> do , służy do tworzenia jednej trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="71eb3-129">Pojedyncza trasa `default` nosi nazwę trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-129">The single route is named `default` route.</span></span> <span data-ttu-id="71eb3-130">Większość aplikacji z kontrolerami i widokami `default` używa szablonu trasy podobnego do trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="71eb3-131">Interfejsy API REST powinny używać [routingu atrybutów](#ar).</span><span class="sxs-lookup"><span data-stu-id="71eb3-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="71eb3-132">Szablon `"{controller=Home}/{action=Index}/{id?}"`trasy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="71eb3-133">Dopasowuje ścieżkę adresu URL, taką jak`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="71eb3-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="71eb3-134">Wyodrębnia wartości `{ controller = Products, action = Details, id = 5 }` trasy przez tokenizowanie ścieżki.</span><span class="sxs-lookup"><span data-stu-id="71eb3-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="71eb3-135">Wyodrębnianie wartości trasy powoduje dopasowanie, jeśli aplikacja `ProductsController` ma `Details` kontroler o nazwie i akcję:</span><span class="sxs-lookup"><span data-stu-id="71eb3-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="71eb3-136">`/Products/Details/5`model wiąże `id = 5` wartość, aby `id` ustawić `5`parametr na .</span><span class="sxs-lookup"><span data-stu-id="71eb3-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="71eb3-137">Zobacz [powiązanie modelu, aby](xref:mvc/models/model-binding) uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="71eb3-138">`{controller=Home}`definiuje `Home` jako domyślny `controller`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="71eb3-139">`{action=Index}`definiuje `Index` jako domyślny `action`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="71eb3-140">Znak `?` w `{id?}` definiuje `id` jako opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="71eb3-141">Domyślne i opcjonalne parametry trasy nie muszą znajdować się w ścieżce adresu URL dopasowania.</span><span class="sxs-lookup"><span data-stu-id="71eb3-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="71eb3-142">Zobacz [Odwołanie do szablonu trasy,](xref:fundamentals/routing#route-template-reference) aby uzyskać szczegółowy opis składni szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="71eb3-143">Pasuje do `/`ścieżki adresu URL .</span><span class="sxs-lookup"><span data-stu-id="71eb3-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="71eb3-144">Tworzy wartości `{ controller = Home, action = Index }`trasy .</span><span class="sxs-lookup"><span data-stu-id="71eb3-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="71eb3-145">Wartości dla `controller` `action` i używać wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="71eb3-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="71eb3-146">`id`nie generuje wartości, ponieważ w ścieżce adresu URL nie ma odpowiedniego segmentu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="71eb3-147">`/`pasuje tylko wtedy, `HomeController` gdy `Index` istnieje a i akcja:</span><span class="sxs-lookup"><span data-stu-id="71eb3-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="71eb3-148">Przy użyciu poprzedniej definicji kontrolera `HomeController.Index` i szablonu trasy akcja jest uruchamiana dla następujących ścieżek adresów URL:</span><span class="sxs-lookup"><span data-stu-id="71eb3-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="71eb3-149">Ścieżka `/` adresu URL używa domyślnych `Home` kontrolerów i `Index` akcji szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="71eb3-150">Ścieżka `/Home` adresu URL używa domyślnej `Index` akcji szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="71eb3-151">Metoda <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>wygody:</span><span class="sxs-lookup"><span data-stu-id="71eb3-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="71eb3-152">Zastępuje:</span><span class="sxs-lookup"><span data-stu-id="71eb3-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="71eb3-153">Routing jest konfigurowany przy użyciu oprogramowania pośredniczącego <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> i. <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*></span><span class="sxs-lookup"><span data-stu-id="71eb3-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="71eb3-154">Aby użyć kontrolerów:</span><span class="sxs-lookup"><span data-stu-id="71eb3-154">To use controllers:</span></span>

* <span data-ttu-id="71eb3-155"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> Wywołanie `UseEndpoints` wewnątrz do [mapowania atrybut routowanych](#ar) kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
* <span data-ttu-id="71eb3-156"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> Wywołanie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>lub , aby mapować [tradycyjnie kierowane](#cr) kontrolery.</span><span class="sxs-lookup"><span data-stu-id="71eb3-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="71eb3-157">Routing konwencjonalny</span><span class="sxs-lookup"><span data-stu-id="71eb3-157">Conventional routing</span></span>

<span data-ttu-id="71eb3-158">Routing konwencjonalny jest używany z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="71eb3-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="71eb3-159">Trasa: `default`</span><span class="sxs-lookup"><span data-stu-id="71eb3-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="71eb3-160">jest przykładem *konwencjonalnej trasy.*</span><span class="sxs-lookup"><span data-stu-id="71eb3-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="71eb3-161">Nazywa się to *routingiem konwencjonalnym,* ponieważ ustanawia *konwencję* dla ścieżek adresów URL:</span><span class="sxs-lookup"><span data-stu-id="71eb3-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="71eb3-162">Pierwszy segment ścieżki, `{controller=Home}`mapuje na nazwę kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="71eb3-163">Drugi segment `{action=Index}`, mapuje na nazwę [akcji.](#action)</span><span class="sxs-lookup"><span data-stu-id="71eb3-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="71eb3-164">Trzeci segment, `{id?}` jest używany `id`do opcjonalnego .</span><span class="sxs-lookup"><span data-stu-id="71eb3-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="71eb3-165">In `?` `{id?}` sprawia, że opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="71eb3-166">`id`służy do mapowania do jednostki modelu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="71eb3-167">Korzystając `default` z tej trasy, ścieżka adresu URL:</span><span class="sxs-lookup"><span data-stu-id="71eb3-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="71eb3-168">`/Products/List`do `ProductsController.List` akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="71eb3-169">`/Blog/Article/17`mapuje `BlogController.Article` i zazwyczaj model `id` wiąże parametr z 17.</span><span class="sxs-lookup"><span data-stu-id="71eb3-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="71eb3-170">To mapowanie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-170">This mapping:</span></span>

* <span data-ttu-id="71eb3-171">Jest oparty **tylko**na kontrolerze i [nazwach akcji](#action) .</span><span class="sxs-lookup"><span data-stu-id="71eb3-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="71eb3-172">Nie jest oparty na obszarach nazw, lokalizacjach plików źródłowych ani parametrach metody.</span><span class="sxs-lookup"><span data-stu-id="71eb3-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="71eb3-173">Korzystanie z routingu konwencjonalnego z trasą domyślną umożliwia tworzenie aplikacji bez konieczności wymyślania nowego wzorca adresu URL dla każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="71eb3-174">W przypadku aplikacji z akcjami stylu [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) która ma spójność adresów URL między kontrolerami:</span><span class="sxs-lookup"><span data-stu-id="71eb3-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="71eb3-175">Pomaga uprościć kod.</span><span class="sxs-lookup"><span data-stu-id="71eb3-175">Helps simplify the code.</span></span>
* <span data-ttu-id="71eb3-176">Sprawia, że interfejs użytkownika bardziej przewidywalne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="71eb3-177">W `id` poprzednim kodzie jest zdefiniowany jako opcjonalny przez szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="71eb3-178">Akcje można wykonywać bez opcjonalnego identyfikatora podanego jako część adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="71eb3-179">Ogólnie rzecz`id` biorąc, gdy zostanie pominięty w adresie URL:</span><span class="sxs-lookup"><span data-stu-id="71eb3-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="71eb3-180">`id`jest ustawiona na `0` powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="71eb3-181">W dopasowywaniu `id == 0`bazy danych nie znaleziono żadnej encji .</span><span class="sxs-lookup"><span data-stu-id="71eb3-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="71eb3-182">[Routing atrybutów](#ar) zapewnia precyzyjną kontrolę, aby identyfikator jest wymagany dla niektórych akcji, a nie dla innych.</span><span class="sxs-lookup"><span data-stu-id="71eb3-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="71eb3-183">Zgodnie z konwencją dokumentacja `id` zawiera parametry opcjonalne, takie jak wtedy, gdy mogą pojawić się w prawidłowym użyciu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="71eb3-184">Większość aplikacji powinna wybrać podstawowy i opisowy schemat routingu, aby adresy URL były czytelne i znaczące.</span><span class="sxs-lookup"><span data-stu-id="71eb3-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="71eb3-185">Domyślna trasa `{controller=Home}/{action=Index}/{id?}`konwencjonalna:</span><span class="sxs-lookup"><span data-stu-id="71eb3-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="71eb3-186">Obsługuje podstawowy i opisowy schemat routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="71eb3-187">Jest użytecznym punktem wyjścia dla aplikacji opartych na interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="71eb3-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="71eb3-188">Jest jedynym szablonem trasy potrzebnym dla wielu aplikacji interfejsu użytkownika sieci Web.</span><span class="sxs-lookup"><span data-stu-id="71eb3-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="71eb3-189">W przypadku większych aplikacji interfejsu użytkownika sieci Web inna trasa przy użyciu [obszarów,](#areas) jeśli często wszystko, co jest potrzebne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="71eb3-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>oraz: <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*></span><span class="sxs-lookup"><span data-stu-id="71eb3-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="71eb3-191">Automatycznie przypisz wartość **zamówienia** do ich punktów końcowych na podstawie kolejności, w jakiej są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="71eb3-192">Routing punktu końcowego w ASP.NET Core 3.0 i nowszych:</span><span class="sxs-lookup"><span data-stu-id="71eb3-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="71eb3-193">Nie ma pojęcia tras.</span><span class="sxs-lookup"><span data-stu-id="71eb3-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="71eb3-194">Nie zapewnia gwarancji zamawiania dla wykonywania rozszerzalności, wszystkie punkty końcowe są przetwarzane jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="71eb3-195">Włącz [rejestrowanie,](xref:fundamentals/logging/index) aby zobaczyć, jak wbudowane implementacje routingu, takie jak <xref:Microsoft.AspNetCore.Routing.Route>, dopasowywanie żądań.</span><span class="sxs-lookup"><span data-stu-id="71eb3-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="71eb3-196">[Routing atrybutów](#ar) jest wyjaśniony w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="71eb3-197">Wiele tras konwencjonalnych</span><span class="sxs-lookup"><span data-stu-id="71eb3-197">Multiple conventional routes</span></span>

<span data-ttu-id="71eb3-198">Wiele [konwencjonalnych tras](#cr) `UseEndpoints` można dodać wewnątrz, dodając więcej połączeń do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> i <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span><span class="sxs-lookup"><span data-stu-id="71eb3-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="71eb3-199">Pozwala to na zdefiniowanie wielu konwencji lub dodanie konwencjonalnych tras dedykowanych określonej [akcji,](#action)takiej jak:</span><span class="sxs-lookup"><span data-stu-id="71eb3-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="71eb3-200">Trasa `blog` w poprzednim kodzie jest **dedykowaną trasą konwencjonalną.**</span><span class="sxs-lookup"><span data-stu-id="71eb3-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="71eb3-201">Nazywa się to dedykowaną trasą konwencjonalną, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="71eb3-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="71eb3-202">Wykorzystuje [konwencjonalne routingu](#cr).</span><span class="sxs-lookup"><span data-stu-id="71eb3-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="71eb3-203">Jest dedykowany do konkretnej [akcji](#action).</span><span class="sxs-lookup"><span data-stu-id="71eb3-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="71eb3-204">Ponieważ `controller` `action` i nie pojawiają się `"blog/{*article}"` w szablonie trasy jako parametry:</span><span class="sxs-lookup"><span data-stu-id="71eb3-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="71eb3-205">Mogą mieć tylko wartości `{ controller = "Blog", action = "Article" }`domyślne .</span><span class="sxs-lookup"><span data-stu-id="71eb3-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="71eb3-206">Ta trasa zawsze jest `BlogController.Article`mapowana do akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="71eb3-207">`/Blog`, `/Blog/Article`i `/Blog/{any-string}` są jedynymi ścieżkami URL, które pasują do trasy blogu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="71eb3-208">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-208">The preceding example:</span></span>

* <span data-ttu-id="71eb3-209">`blog`trasa ma wyższy priorytet dla `default` dopasowań niż trasa, ponieważ jest dodawana jako pierwsza.</span><span class="sxs-lookup"><span data-stu-id="71eb3-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="71eb3-210">Jest i przykład routingu stylu [ślimaka,](https://developer.mozilla.org/docs/Glossary/Slug) gdzie jest to typowe, aby mieć nazwę artykułu jako część adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="71eb3-211">W ASP.NET Core 3.0 i nowszych routing nie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="71eb3-212">Zdefiniuj koncepcję zwaną *trasą*.</span><span class="sxs-lookup"><span data-stu-id="71eb3-212">Define a concept called a *route*.</span></span> <span data-ttu-id="71eb3-213">`UseRouting`dodaje dopasowanie trasy do potoku oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="71eb3-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="71eb3-214">Oprogramowanie `UseRouting` pośredniczące analizuje zestaw punktów końcowych zdefiniowanych w aplikacji i wybiera najlepsze dopasowanie punktu końcowego na podstawie żądania.</span><span class="sxs-lookup"><span data-stu-id="71eb3-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="71eb3-215">Zapewnij gwarancje dotyczące kolejności wykonywania <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozciągliwości, takich jak lub <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span><span class="sxs-lookup"><span data-stu-id="71eb3-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="71eb3-216">Zobacz [Routing](xref:fundamentals/routing) dla materiałów odniesienia na routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="71eb3-217">Konwencjonalna kolejność routingu</span><span class="sxs-lookup"><span data-stu-id="71eb3-217">Conventional routing order</span></span>

<span data-ttu-id="71eb3-218">Routing konwencjonalny pasuje tylko do kombinacji akcji i kontrolera, które są zdefiniowane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="71eb3-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="71eb3-219">Ma to na celu uproszczenie przypadków, w których konwencjonalne trasy nakładają się na siebie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="71eb3-220">Dodawanie tras <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>za <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> pomocą , i automatycznie przypisać wartość zamówienia do swoich punktów końcowych na podstawie kolejności, w jakiej są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="71eb3-221">Dopasowania z trasy, która pojawia się wcześniej mają wyższy priorytet.</span><span class="sxs-lookup"><span data-stu-id="71eb3-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="71eb3-222">Routing konwencjonalny jest zależny od zamówienia.</span><span class="sxs-lookup"><span data-stu-id="71eb3-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="71eb3-223">Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej, ponieważ są bardziej szczegółowe niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="71eb3-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="71eb3-224">[Dedykowane konwencjonalne trasy](#dcr) z `{*article}` połowu wszystkich parametrów trasy, takich jak może uczynić trasę zbyt [chciwy,](xref:fundamentals/routing#greedy)co oznacza, że pasuje do adresów URL, które mają być dopasowane do innych tras.</span><span class="sxs-lookup"><span data-stu-id="71eb3-224">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="71eb3-225">Umieść chciwe trasy później w tabeli tras, aby zapobiec chciwych meczach.</span><span class="sxs-lookup"><span data-stu-id="71eb3-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="71eb3-226">Rozwiązywanie niejednoznacznych działań</span><span class="sxs-lookup"><span data-stu-id="71eb3-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="71eb3-227">Gdy dwa punkty końcowe są zgodne z routingiem, routing musi wykonać jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="71eb3-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="71eb3-228">Wybierz najlepszego kandydata.</span><span class="sxs-lookup"><span data-stu-id="71eb3-228">Choose the best candidate.</span></span>
* <span data-ttu-id="71eb3-229">Zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="71eb3-229">Throw an exception.</span></span>

<span data-ttu-id="71eb3-230">Przykład:</span><span class="sxs-lookup"><span data-stu-id="71eb3-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="71eb3-231">Poprzedni kontroler definiuje dwie akcje, które pasują do:</span><span class="sxs-lookup"><span data-stu-id="71eb3-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="71eb3-232">Ścieżka adresu URL`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="71eb3-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="71eb3-233">Dane `{ controller = Products33, action = Edit, id = 17 }`trasy .</span><span class="sxs-lookup"><span data-stu-id="71eb3-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="71eb3-234">Jest to typowy wzorzec dla kontrolerów MVC:</span><span class="sxs-lookup"><span data-stu-id="71eb3-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="71eb3-235">`Edit(int)`wyświetla formularz do edycji produktu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="71eb3-236">`Edit(int, Product)`przetwarza zaksięgowany formularz.</span><span class="sxs-lookup"><span data-stu-id="71eb3-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="71eb3-237">Aby rozwiązać właściwą trasę:</span><span class="sxs-lookup"><span data-stu-id="71eb3-237">To resolve the correct route:</span></span>

* <span data-ttu-id="71eb3-238">`Edit(int, Product)`jest zaznaczona, gdy `POST`żądanie jest HTTP .</span><span class="sxs-lookup"><span data-stu-id="71eb3-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="71eb3-239">`Edit(int)`jest zaznaczona, gdy [zlecenie HTTP](#verb) jest czymś innym.</span><span class="sxs-lookup"><span data-stu-id="71eb3-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="71eb3-240">`Edit(int)`jest ogólnie wywoływana przez `GET`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="71eb3-241">Aby <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`można było wybrać metodę HTTP żądania, podano program routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="71eb3-242">`HttpPostAttribute` Sprawia, `Edit(int, Product)` że lepsze `Edit(int)`dopasowanie niż .</span><span class="sxs-lookup"><span data-stu-id="71eb3-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="71eb3-243">Ważne jest, aby zrozumieć rolę `HttpPostAttribute`atrybutów, takich jak .</span><span class="sxs-lookup"><span data-stu-id="71eb3-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="71eb3-244">Podobne atrybuty są zdefiniowane dla innych [zleceń HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="71eb3-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="71eb3-245">W [przypadku routingu konwencjonalnego](#cr)często akcje używają tej samej nazwy akcji, gdy są częścią formularza pokazu, przesyłaj przepływ pracy formularza.</span><span class="sxs-lookup"><span data-stu-id="71eb3-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="71eb3-246">Na przykład zobacz [Sprawdzanie dwóch metod akcji edycji](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="71eb3-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="71eb3-247">Jeśli routing nie może wybrać najlepszego kandydata, <xref:System.Reflection.AmbiguousMatchException> jest generowany, lista wielu dopasowanych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="71eb3-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="71eb3-248">Konwencjonalne nazwy tras</span><span class="sxs-lookup"><span data-stu-id="71eb3-248">Conventional route names</span></span>

<span data-ttu-id="71eb3-249">Ciągi `"blog"` i `"default"` w poniższych przykładach są konwencjonalne nazwy tras:</span><span class="sxs-lookup"><span data-stu-id="71eb3-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="71eb3-250">Nazwy trasy nadają trasie nazwę logiczną.</span><span class="sxs-lookup"><span data-stu-id="71eb3-250">The route names give the route a logical name.</span></span> <span data-ttu-id="71eb3-251">Nazwana trasa może służyć do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="71eb3-252">Użycie nazwanej trasy upraszcza tworzenie adresów URL, gdy kolejność tras może skomplikować generowanie adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="71eb3-253">Nazwy tras muszą być unikatowe dla całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="71eb3-254">Nazwy tras:</span><span class="sxs-lookup"><span data-stu-id="71eb3-254">Route names:</span></span>

* <span data-ttu-id="71eb3-255">Nie mają wpływu na dopasowywanie adresów URL lub obsługę żądań.</span><span class="sxs-lookup"><span data-stu-id="71eb3-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="71eb3-256">Są używane tylko do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-256">Are used only for URL generation.</span></span>

<span data-ttu-id="71eb3-257">Koncepcja nazwy trasy jest reprezentowana w routingu jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="71eb3-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="71eb3-258">Nazwa **trasy** terminów i **nazwa punktu końcowego:**</span><span class="sxs-lookup"><span data-stu-id="71eb3-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="71eb3-259">Są wymienne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-259">Are interchangeable.</span></span>
* <span data-ttu-id="71eb3-260">Który z nich jest używany w dokumentacji i kod zależy od interfejsu API są opisane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="71eb3-261">Routing atrybutów dla interfejsów API REST</span><span class="sxs-lookup"><span data-stu-id="71eb3-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="71eb3-262">Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcji aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez [zlecenia HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="71eb3-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="71eb3-263">Routing atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="71eb3-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="71eb3-264">Następujący `StartUp.Configure` kod jest typowy dla interfejsu API REST i jest używany w następnym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="71eb3-265">W poprzednim kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> jest `UseEndpoints` wywoływana wewnątrz do mapowania atrybutu kierowanych kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="71eb3-266">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-266">In the following example:</span></span>

* <span data-ttu-id="71eb3-267">Używana jest `Configure` poprzednia metoda.</span><span class="sxs-lookup"><span data-stu-id="71eb3-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="71eb3-268">`HomeController`dopasowuje zestaw adresów URL podobny `{controller=Home}/{action=Index}/{id?}` do domyślnej trasy konwencjonalnej.</span><span class="sxs-lookup"><span data-stu-id="71eb3-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="71eb3-269">Akcja `HomeController.Index` jest uruchamiana dla dowolnej `/`ścieżki `/Home` `/Home/Index`adresu `/Home/Index/3`URL , , lub .</span><span class="sxs-lookup"><span data-stu-id="71eb3-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="71eb3-270">W tym przykładzie przedstawiono kluczową różnicę programowania między routingiem atrybutów a [routingiem konwencjonalnym](#cr).</span><span class="sxs-lookup"><span data-stu-id="71eb3-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="71eb3-271">Routing atrybutów wymaga więcej danych wejściowych, aby określić trasę.</span><span class="sxs-lookup"><span data-stu-id="71eb3-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="71eb3-272">Konwencjonalna trasa domyślna obsługuje trasy bardziej zwięźle.</span><span class="sxs-lookup"><span data-stu-id="71eb3-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="71eb3-273">Jednak routing atrybutów umożliwia i wymaga precyzyjnej kontroli, które szablony tras mają zastosowanie do każdej [akcji](#action).</span><span class="sxs-lookup"><span data-stu-id="71eb3-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="71eb3-274">W routingu atrybutów nazwa kontrolera i nazwy akcji nie odgrywają **żadnej** roli, w której akcja jest dopasowywała.</span><span class="sxs-lookup"><span data-stu-id="71eb3-274">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="71eb3-275">Poniższy przykład pasuje do tych samych adresów URL, co w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="71eb3-276">Poniższy kod używa zastępowania tokenów `action` i: `controller`</span><span class="sxs-lookup"><span data-stu-id="71eb3-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="71eb3-277">Następujący kod ma `[Route("[controller]/[action]")]` zastosowanie do kontrolera:</span><span class="sxs-lookup"><span data-stu-id="71eb3-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="71eb3-278">W poprzednim kodzie `Index` szablony metod muszą `/` `~/` być dołączane lub do szablonów marszruty.</span><span class="sxs-lookup"><span data-stu-id="71eb3-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="71eb3-279">Szablony tras zastosowane do akcji, `/` `~/` które rozpoczynają się lub nie są łączone z szablonami tras zastosowanymi do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="71eb3-280">Zobacz [Pierwszeństwo szablonu trasy, aby](xref:fundamentals/routing#rtp) uzyskać informacje o wyborze szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="71eb3-281">Nazwy marszruty zarezerwowane</span><span class="sxs-lookup"><span data-stu-id="71eb3-281">Reserved routing names</span></span>

<span data-ttu-id="71eb3-282">Następujące słowa kluczowe to nazwy parametrów trasy zastrzeżonej podczas korzystania z kontrolerów lub stron razor:</span><span class="sxs-lookup"><span data-stu-id="71eb3-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="71eb3-283">Użycie `page` jako parametru trasy z routingiem atrybutów jest częstym błędem.</span><span class="sxs-lookup"><span data-stu-id="71eb3-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="71eb3-284">Powoduje to niespójne i mylące zachowanie z generowaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="71eb3-285">Nazwy parametrów specjalnych są używane przez generowanie adresów URL w celu ustalenia, czy operacja generowania adresów URL odnosi się do strony Razor lub kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="71eb3-286">Szablony czasowników HTTP</span><span class="sxs-lookup"><span data-stu-id="71eb3-286">HTTP verb templates</span></span>

<span data-ttu-id="71eb3-287">ASP.NET Core ma następujące szablony czasowników HTTP:</span><span class="sxs-lookup"><span data-stu-id="71eb3-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="71eb3-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="71eb3-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="71eb3-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="71eb3-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="71eb3-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="71eb3-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="71eb3-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="71eb3-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="71eb3-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="71eb3-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="71eb3-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="71eb3-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="71eb3-294">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="71eb3-294">Route templates</span></span>

<span data-ttu-id="71eb3-295">ASP.NET Core ma następujące szablony tras:</span><span class="sxs-lookup"><span data-stu-id="71eb3-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="71eb3-296">Wszystkie [szablony zleceń HTTP](#verb) są szablonami marszrut.</span><span class="sxs-lookup"><span data-stu-id="71eb3-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="71eb3-297">[[Trasa]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="71eb3-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="71eb3-298">Routing atrybutów z atrybutami zlecenia http</span><span class="sxs-lookup"><span data-stu-id="71eb3-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="71eb3-299">Należy wziąć pod uwagę następujący kontroler:</span><span class="sxs-lookup"><span data-stu-id="71eb3-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="71eb3-300">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-300">In the preceding code:</span></span>

* <span data-ttu-id="71eb3-301">Każda akcja `[HttpGet]` zawiera atrybut, który ogranicza dopasowywanie tylko do żądań HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="71eb3-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="71eb3-302">Akcja `GetProduct` zawiera `"{id}"` szablon, w `id` związku z tym `"api/[controller]"` jest dołączany do szablonu na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="71eb3-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="71eb3-303">Szablon metody `"api/[controller]/"{id}""`jest .</span><span class="sxs-lookup"><span data-stu-id="71eb3-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="71eb3-304">W związku z tym ta akcja `/api/test2/xyz`jest`/api/test2/123``/api/test2/{any string}`zgodna tylko z żądaniami GET dla formularza , , itp.</span><span class="sxs-lookup"><span data-stu-id="71eb3-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="71eb3-305">Akcja `GetIntProduct` zawiera `"int/{id:int}")` szablon.</span><span class="sxs-lookup"><span data-stu-id="71eb3-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="71eb3-306">Część `:int` szablonu ogranicza wartości `id` trasy do ciągów, które można przekonwertować na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="71eb3-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="71eb3-307">Wniosek GET `/api/test2/int/abc`do:</span><span class="sxs-lookup"><span data-stu-id="71eb3-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="71eb3-308">Nie pasuje do tej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="71eb3-309">Zwraca błąd [404 Nie znaleziono.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)</span><span class="sxs-lookup"><span data-stu-id="71eb3-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="71eb3-310">Akcja `GetInt2Product` zawiera `{id}` w szablonie, ale `id` nie ogranicza się do wartości, które mogą być konwertowane na całkowitą.</span><span class="sxs-lookup"><span data-stu-id="71eb3-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="71eb3-311">Wniosek GET `/api/test2/int2/abc`do:</span><span class="sxs-lookup"><span data-stu-id="71eb3-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="71eb3-312">Pasuje do tej trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-312">Matches this route.</span></span>
  * <span data-ttu-id="71eb3-313">Powiązanie modelu `abc` nie można przekonwertować na całkowitą.</span><span class="sxs-lookup"><span data-stu-id="71eb3-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="71eb3-314">Parametrem `id` metody jest liczba całkowita.</span><span class="sxs-lookup"><span data-stu-id="71eb3-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="71eb3-315">Zwraca [400 Bad Request,](https://developer.mozilla.org/docs/Web/HTTP/Status/400) ponieważ powiązanie modelu nie można przekonwertować`abc` na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="71eb3-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="71eb3-316">Routing atrybutów <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> może używać <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>atrybutów, takich jak , i <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span><span class="sxs-lookup"><span data-stu-id="71eb3-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="71eb3-317">Wszystkie atrybuty [zlecenia HTTP](#verb) akceptują szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="71eb3-318">W poniższym przykładzie przedstawiono dwie akcje pasujące do tego samego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="71eb3-319">Korzystanie ze `/products3`ścieżki adresu URL:</span><span class="sxs-lookup"><span data-stu-id="71eb3-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="71eb3-320">Akcja `MyProductsController.ListProducts` jest uruchamiana, gdy `GET`zlecenie [HTTP](#verb) jest .</span><span class="sxs-lookup"><span data-stu-id="71eb3-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="71eb3-321">Akcja `MyProductsController.CreateProduct` jest uruchamiana, gdy `POST`zlecenie [HTTP](#verb) jest .</span><span class="sxs-lookup"><span data-stu-id="71eb3-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="71eb3-322">Podczas tworzenia interfejsu API REST, rzadko, że trzeba `[Route(...)]` będzie użyć w metodzie akcji, ponieważ akcja akceptuje wszystkie metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="71eb3-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="71eb3-323">Lepiej jest użyć bardziej szczegółowego [atrybutu zlecenia HTTP,](#verb) aby dokładnie określić, co obsługuje interfejs API.</span><span class="sxs-lookup"><span data-stu-id="71eb3-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="71eb3-324">Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i zlecenia HTTP są mapowane na określone operacje logiczne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="71eb3-325">Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcji aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez zlecenia HTTP.</span><span class="sxs-lookup"><span data-stu-id="71eb3-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="71eb3-326">Oznacza to, że wiele operacji, na przykład GET i POST na tym samym zasobie logicznym, używa tego samego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="71eb3-327">Routing atrybutów zapewnia poziom kontroli, który jest potrzebny do starannego projektowania układu publicznego punktu końcowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="71eb3-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="71eb3-328">Ponieważ trasa atrybutu ma zastosowanie do określonej akcji, łatwo jest wprowadzić parametry wymagane jako część definicji szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="71eb3-329">W poniższym `id` przykładzie jest wymagane jako część ścieżki adresu URL:</span><span class="sxs-lookup"><span data-stu-id="71eb3-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="71eb3-330">Akcja: `Products2ApiController.GetProduct(int)`</span><span class="sxs-lookup"><span data-stu-id="71eb3-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="71eb3-331">Jest uruchamiany ze ścieżką URL, taką jak`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="71eb3-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="71eb3-332">Nie jest uruchamiany ze `/products2`ścieżką adresu URL .</span><span class="sxs-lookup"><span data-stu-id="71eb3-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="71eb3-333">[Atrybut [Zużywa]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) umożliwia akcję, aby ograniczyć typy zawartości obsługiwanych żądań.</span><span class="sxs-lookup"><span data-stu-id="71eb3-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="71eb3-334">Aby uzyskać więcej informacji, zobacz [Definiowanie obsługiwanych typów zawartości żądań za pomocą atrybutu Zużywa](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="71eb3-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="71eb3-335">Zobacz [Routing,](xref:fundamentals/routing) aby uzyskać pełny opis szablonów tras i powiązanych opcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="71eb3-336">Aby uzyskać `[ApiController]`więcej informacji na temat , zobacz [ApiController atrybut](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="71eb3-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="71eb3-337">Nazwa trasy</span><span class="sxs-lookup"><span data-stu-id="71eb3-337">Route name</span></span>

<span data-ttu-id="71eb3-338">Poniższy kod definiuje nazwę `Products_List`trasy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="71eb3-339">Nazwy tras mogą być używane do generowania adresu URL na podstawie określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="71eb3-340">Nazwy tras:</span><span class="sxs-lookup"><span data-stu-id="71eb3-340">Route names:</span></span>

* <span data-ttu-id="71eb3-341">Nie mają wpływu na zachowanie dopasowywania adresu URL routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="71eb3-342">Są używane tylko do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-342">Are only used for URL generation.</span></span>

<span data-ttu-id="71eb3-343">Nazwy tras muszą być unikatowe dla całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="71eb3-344">Kontrast poprzedniego kodu z konwencjonalną trasą domyślną, `id` która`{id?}`definiuje parametr jako opcjonalny ( ).</span><span class="sxs-lookup"><span data-stu-id="71eb3-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="71eb3-345">Możliwość precyzyjnego określania interfejsów API ma zalety, takie jak zezwalanie `/products` i `/products/5` wysyłanie do różnych akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="71eb3-346">Łączenie tras atrybutów</span><span class="sxs-lookup"><span data-stu-id="71eb3-346">Combining attribute routes</span></span>

<span data-ttu-id="71eb3-347">Aby routing atrybutów był mniej powtarzalny, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="71eb3-348">Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzane do szablonów tras w akcjach.</span><span class="sxs-lookup"><span data-stu-id="71eb3-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="71eb3-349">Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w routingu atrybutu używanego przez kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="71eb3-350">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-350">In the preceding example:</span></span>

* <span data-ttu-id="71eb3-351">Ścieżka `/products` adresu URL może być zgodna`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="71eb3-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="71eb3-352">Ścieżka `/products/5` adresu URL `ProductsApi.GetProduct(int)`może być zgodna .</span><span class="sxs-lookup"><span data-stu-id="71eb3-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="71eb3-353">Obie te akcje są `GET` zgodne tylko z `[HttpGet]` protokołem HTTP, ponieważ są oznaczone atrybutem.</span><span class="sxs-lookup"><span data-stu-id="71eb3-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="71eb3-354">Szablony tras zastosowane do akcji, `/` `~/` które rozpoczynają się lub nie są łączone z szablonami tras zastosowanymi do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="71eb3-355">Poniższy przykład pasuje do zestawu ścieżek URL podobnych do trasy domyślnej.</span><span class="sxs-lookup"><span data-stu-id="71eb3-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="71eb3-356">W poniższej `[Route]` tabeli opisano atrybuty w poprzednim kodzie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="71eb3-357">Atrybut</span><span class="sxs-lookup"><span data-stu-id="71eb3-357">Attribute</span></span>               | <span data-ttu-id="71eb3-358">Łączy się z`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="71eb3-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="71eb3-359">Definiuje szablon trasy</span><span class="sxs-lookup"><span data-stu-id="71eb3-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="71eb3-360">Tak</span><span class="sxs-lookup"><span data-stu-id="71eb3-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="71eb3-361">Tak</span><span class="sxs-lookup"><span data-stu-id="71eb3-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="71eb3-362">**Nie**</span><span class="sxs-lookup"><span data-stu-id="71eb3-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="71eb3-363">Tak</span><span class="sxs-lookup"><span data-stu-id="71eb3-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="71eb3-364">Kolejność marszruty atrybutów</span><span class="sxs-lookup"><span data-stu-id="71eb3-364">Attribute route order</span></span>

<span data-ttu-id="71eb3-365">Routing tworzy drzewo i dopasowuje wszystkie punkty końcowe jednocześnie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="71eb3-366">Wpisy trasy zachowują się tak, jakby były umieszczone w idealnym kolejności.</span><span class="sxs-lookup"><span data-stu-id="71eb3-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="71eb3-367">Najbardziej konkretne trasy mają szansę wykonać przed bardziej ogólnych tras.</span><span class="sxs-lookup"><span data-stu-id="71eb3-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="71eb3-368">Na przykład trasa atrybutu, taka jak, `blog/search/{topic}` jest `blog/{*article}`bardziej szczegółowa niż trasa atrybutu, taka jak .</span><span class="sxs-lookup"><span data-stu-id="71eb3-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="71eb3-369">Trasa `blog/search/{topic}` ma wyższy priorytet, domyślnie, ponieważ jest bardziej szczegółowe.</span><span class="sxs-lookup"><span data-stu-id="71eb3-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="71eb3-370">Przy użyciu [konwencjonalnego routingu](#cr)deweloper jest odpowiedzialny za umieszczanie tras w żądanej kolejności.</span><span class="sxs-lookup"><span data-stu-id="71eb3-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="71eb3-371">Trasy atrybutów można skonfigurować <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> zamówienie przy użyciu właściwości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="71eb3-372">Wszystkie [atrybuty trasy](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) pod `Order` warunkiem struktury obejmują .</span><span class="sxs-lookup"><span data-stu-id="71eb3-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="71eb3-373">Trasy są przetwarzane zgodnie z rosnącym `Order` rodzajem właściwości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="71eb3-374">Domyślna kolejność `0`to .</span><span class="sxs-lookup"><span data-stu-id="71eb3-374">The default order is `0`.</span></span> <span data-ttu-id="71eb3-375">Ustawianie trasy `Order = -1` przy użyciu przebiegów przed trasami, które nie ustawiają zamówienia.</span><span class="sxs-lookup"><span data-stu-id="71eb3-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="71eb3-376">Ustawianie trasy `Order = 1` przy użyciu przebiegów po domyślnym uporządkowaniu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="71eb3-377">**Unikaj** w `Order`zależności od .</span><span class="sxs-lookup"><span data-stu-id="71eb3-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="71eb3-378">Jeśli miejsce adresu URL aplikacji wymaga jawnych wartości zamówienia do prawidłowego rozsyłania, prawdopodobnie jest to również mylące dla klientów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="71eb3-379">Ogólnie rzecz biorąc routing atrybutów wybiera prawidłową trasę z dopasowaniem adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="71eb3-380">Jeśli domyślna kolejność używana do generowania adresów URL nie działa, użycie nazwy trasy `Order` jako zastąpienia jest zwykle prostsze niż zastosowanie właściwości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="71eb3-381">Rozważmy następujące dwa kontrolery, które `/home`definiują dopasowanie trasy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="71eb3-382">Żądanie `/home` z poprzednim kodem zgłasza wyjątek podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="71eb3-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="71eb3-383">Dodanie `Order` do jednego z atrybutów trasy rozwiązuje niejednoznaczność:</span><span class="sxs-lookup"><span data-stu-id="71eb3-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="71eb3-384">Za pomocą poprzedniego `/home` kodu `HomeController.Index` uruchamia punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="71eb3-385">Aby dostać `MyDemoController.MyIndex`się `/home/MyIndex`do , wniosek .</span><span class="sxs-lookup"><span data-stu-id="71eb3-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="71eb3-386">**Uwaga:**</span><span class="sxs-lookup"><span data-stu-id="71eb3-386">**Note**:</span></span>

* <span data-ttu-id="71eb3-387">Powyższy kod jest przykładem lub złym projektem routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="71eb3-388">Został on użyty `Order` do zilustrowania nieruchomości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="71eb3-389">Właściwość `Order` rozwiązuje tylko niejednoznaczności, tego szablonu nie można dopasować.</span><span class="sxs-lookup"><span data-stu-id="71eb3-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="71eb3-390">Byłoby lepiej, aby `[Route("Home")]` usunąć szablon.</span><span class="sxs-lookup"><span data-stu-id="71eb3-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="71eb3-391">Zobacz [Konwencje trasy i aplikacji Razor Pages: Kolejność tras, aby](xref:razor-pages/razor-pages-conventions#route-order) uzyskać informacje o zamówieniu trasy za pomocą strony Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="71eb3-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="71eb3-392">W niektórych przypadkach zwracany jest błąd HTTP 500 z niejednoznacznymi trasami.</span><span class="sxs-lookup"><span data-stu-id="71eb3-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="71eb3-393">Użyj [rejestrowania,](xref:fundamentals/logging/index) aby zobaczyć, `AmbiguousMatchException`które punkty końcowe spowodowały .</span><span class="sxs-lookup"><span data-stu-id="71eb3-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="71eb3-394">Zastępowanie tokenów w szablonach tras [kontroler], [akcja], [obszar]</span><span class="sxs-lookup"><span data-stu-id="71eb3-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="71eb3-395">Dla wygody trasy atrybutów obsługują zastępowanie tokenów zarezerwowanych parametrów trasy, załączając token w jednym z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="71eb3-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="71eb3-396">Szelki kwadratowe:`[]`</span><span class="sxs-lookup"><span data-stu-id="71eb3-396">Square braces: `[]`</span></span>
* <span data-ttu-id="71eb3-397">Kręcone szelki:`{}`</span><span class="sxs-lookup"><span data-stu-id="71eb3-397">Curly braces: `{}`</span></span>

<span data-ttu-id="71eb3-398">Tokeny `[action]`, `[area]`i `[controller]` są zastępowane wartościami nazwy akcji, nazwy obszaru i nazwy kontrolera z akcji, w której trasa jest zdefiniowana:</span><span class="sxs-lookup"><span data-stu-id="71eb3-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="71eb3-399">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="71eb3-400">Pasuje`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="71eb3-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="71eb3-401">Pasuje`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="71eb3-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="71eb3-402">Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="71eb3-403">W poprzednim przykładzie zachowuje się tak samo jak następujący kod:</span><span class="sxs-lookup"><span data-stu-id="71eb3-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="71eb3-404">Trasy atrybutów można również łączyć z dziedziczeniem.</span><span class="sxs-lookup"><span data-stu-id="71eb3-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="71eb3-405">Jest to potężne w połączeniu z zastąpieniem tokenów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="71eb3-406">Zastępowanie tokenów ma również zastosowanie do nazw tras zdefiniowanych przez trasy atrybutów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="71eb3-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji:</span><span class="sxs-lookup"><span data-stu-id="71eb3-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="71eb3-408">Zastępowanie tokenów ma również zastosowanie do nazw tras zdefiniowanych przez trasy atrybutów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="71eb3-409">generuje unikatową nazwę trasy dla każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="71eb3-410">Aby dopasować dosłowny `[` ogranicznik zastępujący token lub `]`, ucieknij go, powtarzając znak (`[[` lub `]]`).</span><span class="sxs-lookup"><span data-stu-id="71eb3-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="71eb3-411">Dostosowywanie zastępowania tokenów za pomocą transformatora parametrów</span><span class="sxs-lookup"><span data-stu-id="71eb3-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="71eb3-412">Zastąpienie tokenu można dostosować za pomocą transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="71eb3-413">Transformator parametrów <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> implementuje i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="71eb3-414">Na przykład transformator parametrów `SlugifyParameterTransformer` `SubscriptionManagement` niestandardowych `subscription-management`zmienia wartość trasy na:</span><span class="sxs-lookup"><span data-stu-id="71eb3-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="71eb3-415">Jest <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> to konwencja modelu aplikacji, która:</span><span class="sxs-lookup"><span data-stu-id="71eb3-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="71eb3-416">Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="71eb3-417">Dostosowuje wartości tokenu marszruty atrybutu podczas ich zastępowyjca.</span><span class="sxs-lookup"><span data-stu-id="71eb3-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="71eb3-418">Poprzednia `ListAll` metoda `/subscription-management/list-all`jest zgodna .</span><span class="sxs-lookup"><span data-stu-id="71eb3-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="71eb3-419">Jest `RouteTokenTransformerConvention` zarejestrowany jako opcja `ConfigureServices`w .</span><span class="sxs-lookup"><span data-stu-id="71eb3-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="71eb3-420">Zobacz [dokumenty sieci web MDN na ślimaku,](https://developer.mozilla.org/docs/Glossary/Slug) aby uzyskać definicję ślimaka.</span><span class="sxs-lookup"><span data-stu-id="71eb3-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="71eb3-421">Wiele tras atrybutów</span><span class="sxs-lookup"><span data-stu-id="71eb3-421">Multiple attribute routes</span></span>

<span data-ttu-id="71eb3-422">Routing atrybutów obsługuje definiowanie wielu tras, które osiągają tę samą akcję.</span><span class="sxs-lookup"><span data-stu-id="71eb3-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="71eb3-423">Najczęstszym zastosowaniem tego jest naśladować zachowanie domyślnej trasy konwencjonalnej, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="71eb3-424">Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każdy z nich łączy się z każdym z atrybutów trasy w metodach akcji:</span><span class="sxs-lookup"><span data-stu-id="71eb3-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="71eb3-425">Implementują `IActionConstraint`wszystkie ograniczenia trasy [czasowników HTTP](#verb) .</span><span class="sxs-lookup"><span data-stu-id="71eb3-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="71eb3-426">Gdy wiele atrybutów <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> trasy, które implementują są umieszczane w akcji:</span><span class="sxs-lookup"><span data-stu-id="71eb3-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="71eb3-427">Każde ograniczenie akcji łączy się z szablonem trasy zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="71eb3-428">Korzystanie z wielu tras w akcjach może wydawać się przydatne i zaawansowane, lepiej zachować podstawową i dobrze zdefiniowaną przestrzeń adresową aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="71eb3-429">Użyj wielu tras w akcjach **tylko** wtedy, gdy jest to potrzebne, na przykład do obsługi istniejących klientów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="71eb3-430">Określanie parametrów opcjonalnych trasy atrybutu, wartości domyślnych i ograniczeń</span><span class="sxs-lookup"><span data-stu-id="71eb3-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="71eb3-431">Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="71eb3-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="71eb3-432">W poprzednim kodzie `[HttpPost("product/{id:int}")]` stosuje ograniczenie trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="71eb3-433">Akcja `ProductsController.ShowProduct` jest dopasowywała się `/product/3`tylko do ścieżek URL, takich jak .</span><span class="sxs-lookup"><span data-stu-id="71eb3-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="71eb3-434">Część `{id:int}` szablonu marszruty ogranicza ten segment tylko do liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="71eb3-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="71eb3-435">Zobacz [Odwołanie do szablonu trasy,](xref:fundamentals/routing#route-template-reference) aby uzyskać szczegółowy opis składni szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="71eb3-436">Niestandardowe atrybuty trasy przy użyciu IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="71eb3-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="71eb3-437">Implementują wszystkie [atrybuty trasy](#rt) <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span><span class="sxs-lookup"><span data-stu-id="71eb3-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="71eb3-438">ASP.NET podstawowy czas pracy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="71eb3-439">Wyszukuje atrybuty na klasy kontrolera i metody akcji po uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="71eb3-440">Używa atrybutów, `IRouteTemplateProvider` które implementują do tworzenia początkowego zestawu tras.</span><span class="sxs-lookup"><span data-stu-id="71eb3-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="71eb3-441">Implementuj `IRouteTemplateProvider` do definiowania atrybutów trasy niestandardowej.</span><span class="sxs-lookup"><span data-stu-id="71eb3-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="71eb3-442">Każdy `IRouteTemplateProvider` z nich umożliwia zdefiniowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:</span><span class="sxs-lookup"><span data-stu-id="71eb3-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="71eb3-443">Poprzednia `Get` metoda `Order = 2, Template = api/MyTestApi`zwraca .</span><span class="sxs-lookup"><span data-stu-id="71eb3-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="71eb3-444">Dostosowywanie tras atrybutów za pomocą modelu aplikacji</span><span class="sxs-lookup"><span data-stu-id="71eb3-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="71eb3-445">Model aplikacji:</span><span class="sxs-lookup"><span data-stu-id="71eb3-445">The application model:</span></span>

* <span data-ttu-id="71eb3-446">Jest modelem obiektów utworzonym podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="71eb3-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="71eb3-447">Zawiera wszystkie metadane używane przez ASP.NET Core do kierowania i wykonywania akcji w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="71eb3-448">Model aplikacji zawiera wszystkie dane zebrane z atrybutów trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="71eb3-449">Dane z atrybutów trasy `IRouteTemplateProvider` jest dostarczany przez implementację.</span><span class="sxs-lookup"><span data-stu-id="71eb3-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="71eb3-450">Konwencje:</span><span class="sxs-lookup"><span data-stu-id="71eb3-450">Conventions:</span></span>

* <span data-ttu-id="71eb3-451">Można zapisać, aby zmodyfikować model aplikacji, aby dostosować zachowanie routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="71eb3-452">Są odczytywane przy uruchamianiu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-452">Are read at app startup.</span></span>

<span data-ttu-id="71eb3-453">W tej sekcji przedstawiono podstawowy przykład dostosowywania routingu przy użyciu modelu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="71eb3-454">Poniższy kod sprawia, że trasy są z grubsza zgodne ze strukturą folderów projektu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="71eb3-455">Poniższy kod uniemożliwia `namespace` stosowanie konwencji do kontrolerów, które są kierowane atrybutem:</span><span class="sxs-lookup"><span data-stu-id="71eb3-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="71eb3-456">Na przykład następujący kontroler nie `NamespaceRoutingConvention`używa:</span><span class="sxs-lookup"><span data-stu-id="71eb3-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="71eb3-457">Metoda: `NamespaceRoutingConvention.Apply`</span><span class="sxs-lookup"><span data-stu-id="71eb3-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="71eb3-458">Nic nie robi, jeśli kontroler jest kierowane atrybut.</span><span class="sxs-lookup"><span data-stu-id="71eb3-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="71eb3-459">Ustawia szablon kontrolerów na `namespace`podstawie , `namespace` z usuniętą bazą.</span><span class="sxs-lookup"><span data-stu-id="71eb3-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="71eb3-460">Można `NamespaceRoutingConvention` zastosować w: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="71eb3-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="71eb3-461">Rozważmy na przykład następujący kontroler:</span><span class="sxs-lookup"><span data-stu-id="71eb3-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="71eb3-462">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-462">In the preceding code:</span></span>

* <span data-ttu-id="71eb3-463">Podstawą `namespace` jest `My.Application`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="71eb3-464">Pełna nazwa poprzedniego kontrolera `My.Application.Admin.Controllers.UsersController`to .</span><span class="sxs-lookup"><span data-stu-id="71eb3-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="71eb3-465">Szablon `NamespaceRoutingConvention` kontrolerów ma `Admin/Controllers/Users/[action]/{id?`być .</span><span class="sxs-lookup"><span data-stu-id="71eb3-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="71eb3-466">Można `NamespaceRoutingConvention` również zastosować jako atrybut na kontrolerze:</span><span class="sxs-lookup"><span data-stu-id="71eb3-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="71eb3-467">Routing mieszany: routing atrybutów a routing konwencjonalny</span><span class="sxs-lookup"><span data-stu-id="71eb3-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="71eb3-468">ASP.NET aplikacje Core mogą łączyć użycie konwencjonalnego routingu i routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="71eb3-469">Typowe jest używanie konwencjonalnych tras dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.</span><span class="sxs-lookup"><span data-stu-id="71eb3-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="71eb3-470">Akcje są kierowane konwencjonalnie lub są kierowane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="71eb3-471">Umieszczenie trasy na kontrolerze lub akcji sprawia, że atrybut jest kierowany.</span><span class="sxs-lookup"><span data-stu-id="71eb3-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="71eb3-472">Akcje definiujące trasy atrybutów nie mogą być osiągane za pośrednictwem tras konwencjonalnych i odwrotnie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="71eb3-473">**Każdy** atrybut trasy na kontrolerze sprawia, że **wszystkie** akcje w atrybucie kontrolera kierowane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="71eb3-474">Routing atrybutów i routing konwencjonalny używają tego samego aparatu routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="71eb3-475">Generowanie adresów URL i wartości otoczenia</span><span class="sxs-lookup"><span data-stu-id="71eb3-475">URL Generation and ambient values</span></span>

<span data-ttu-id="71eb3-476">Aplikacje mogą używać funkcji generowania adresów URL routingu do generowania łączy url do akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="71eb3-477">Generowanie adresów URL eliminuje twarde kodowanie adresów URL, dzięki czemu kod jest bardziej niezawodny i niezawodny.</span><span class="sxs-lookup"><span data-stu-id="71eb3-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="71eb3-478">W tej sekcji skupiono się na funkcjach generowania adresów URL udostępnianych przez MVC i opisano tylko podstawy działania generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="71eb3-479">Zobacz [Routing, aby](xref:fundamentals/routing) uzyskać szczegółowy opis generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="71eb3-480">Interfejs <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> jest podstawowym elementem infrastruktury między MVC i routingu do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="71eb3-481">Wystąpienie `IUrlHelper` jest dostępne `Url` za pośrednictwem właściwości w kontrolerach, widokach i składnikach widoku.</span><span class="sxs-lookup"><span data-stu-id="71eb3-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="71eb3-482">W poniższym przykładzie `IUrlHelper` interfejs jest `Controller.Url` używany za pośrednictwem właściwości do generowania adresu URL do innej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="71eb3-483">Jeśli aplikacja używa domyślnej trasy konwencjonalnej, `url` wartością zmiennej `/UrlGeneration/Destination`jest ciąg ścieżki adresu URL .</span><span class="sxs-lookup"><span data-stu-id="71eb3-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="71eb3-484">Ta ścieżka adresu URL jest tworzona przez routing przez połączenie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="71eb3-485">Wartości trasy z bieżącego żądania, które są **nazywane wartościami otoczenia**.</span><span class="sxs-lookup"><span data-stu-id="71eb3-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="71eb3-486">Wartości przekazywane `Url.Action` do i zastępowanie tych wartości w szablonie trasy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="71eb3-487">Każdy parametr trasy w szablonie trasy ma swoją wartość zastąpiona pasującymi nazwami z wartościami i wartościami otoczenia.</span><span class="sxs-lookup"><span data-stu-id="71eb3-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="71eb3-488">Parametr trasy, który nie ma wartości, może:</span><span class="sxs-lookup"><span data-stu-id="71eb3-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="71eb3-489">Użyj wartości domyślnej, jeśli ją ma.</span><span class="sxs-lookup"><span data-stu-id="71eb3-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="71eb3-490">Być pominięte, jeśli jest to opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-490">Be skipped if it's optional.</span></span> <span data-ttu-id="71eb3-491">Na przykład `id` z szablonu `{controller}/{action}/{id?}`trasy .</span><span class="sxs-lookup"><span data-stu-id="71eb3-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="71eb3-492">Generowanie adresu URL kończy się niepowodzeniem, jeśli dowolny wymagany parametr trasy nie ma odpowiedniej wartości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="71eb3-493">Jeśli generowanie adresu URL nie powiedzie się dla trasy, następna trasa jest wypróbowywych, dopóki nie zostanie wypróbowane wszystkie trasy lub zostanie znalezione dopasowanie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="71eb3-494">W poprzednim przykładzie `Url.Action` zakłada się [konwencjonalną routing](#cr).</span><span class="sxs-lookup"><span data-stu-id="71eb3-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="71eb3-495">Generowanie adresów URL działa podobnie z [routingiem atrybutów,](#ar)chociaż pojęcia są różne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="71eb3-496">Z konwencjonalną trasą:</span><span class="sxs-lookup"><span data-stu-id="71eb3-496">With conventional routing:</span></span>

* <span data-ttu-id="71eb3-497">Wartości trasy są używane do rozwijania szablonu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="71eb3-498">Wartości trasy `controller` dla `action` i zwykle pojawiają się w tym szablonie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="71eb3-499">Działa to, ponieważ adresy URL dopasowane przez routing są zgodne z konwencją.</span><span class="sxs-lookup"><span data-stu-id="71eb3-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="71eb3-500">W poniższym przykładzie użyto routingu atrybutów:</span><span class="sxs-lookup"><span data-stu-id="71eb3-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="71eb3-501">Akcja `Source` w poprzednim kodzie `custom/url/to/destination`generuje .</span><span class="sxs-lookup"><span data-stu-id="71eb3-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="71eb3-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>w ASP.NET Core 3.0 jako alternatywa `IUrlHelper`dla .</span><span class="sxs-lookup"><span data-stu-id="71eb3-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="71eb3-503">`LinkGenerator`oferuje podobną, ale bardziej elastyczną funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="71eb3-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="71eb3-504">Każda metoda `IUrlHelper` na ma odpowiednią `LinkGenerator` rodzinę metod, jak również.</span><span class="sxs-lookup"><span data-stu-id="71eb3-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="71eb3-505">Generowanie adresów URL według nazwy akcji</span><span class="sxs-lookup"><span data-stu-id="71eb3-505">Generating URLs by action name</span></span>

<span data-ttu-id="71eb3-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)i wszystkie powiązane przeciążenia są przeznaczone do generowania docelowego punktu końcowego przez określenie nazwy kontrolera i nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="71eb3-507">Podczas `Url.Action`korzystania z , `controller` bieżące wartości trasy dla i `action` są dostarczane przez środowisko wykonawcze:</span><span class="sxs-lookup"><span data-stu-id="71eb3-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="71eb3-508">Wartość `controller` i `action` są częścią wartości [otoczenia](#ambient) i wartości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="71eb3-509">Metoda `Url.Action` zawsze używa bieżących `action` wartości `controller` i generuje ścieżkę adresu URL, która kieruje do bieżącej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="71eb3-510">Routing próbuje użyć wartości w wartościach otoczenia, aby wypełnić informacje, które nie zostały podane podczas generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="71eb3-511">Rozważ trasę `{a}/{b}/{c}/{d}` podobną `{ a = Alice, b = Bob, c = Carol, d = David }`do wartości otoczenia:</span><span class="sxs-lookup"><span data-stu-id="71eb3-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="71eb3-512">Routing ma wystarczającą ilość informacji, aby wygenerować adres URL bez żadnych dodatkowych wartości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="71eb3-513">Routing ma wystarczającą ilość informacji, ponieważ wszystkie parametry trasy mają wartość.</span><span class="sxs-lookup"><span data-stu-id="71eb3-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="71eb3-514">Jeśli wartość `{ d = Donovan }` zostanie dodana:</span><span class="sxs-lookup"><span data-stu-id="71eb3-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="71eb3-515">Wartość `{ d = David }` jest ignorowana.</span><span class="sxs-lookup"><span data-stu-id="71eb3-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="71eb3-516">Wygenerowana ścieżka `Alice/Bob/Carol/Donovan`adresu URL to .</span><span class="sxs-lookup"><span data-stu-id="71eb3-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="71eb3-517">**Ostrzeżenie:** ścieżki adresów URL są hierarchiczne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="71eb3-518">W poprzednim przykładzie, jeśli `{ c = Cheryl }` wartość jest dodana:</span><span class="sxs-lookup"><span data-stu-id="71eb3-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="71eb3-519">Obie wartości `{ c = Carol, d = David }` są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="71eb3-520">Nie ma już wartości `d` dla i generowania adresów URL nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="71eb3-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="71eb3-521">Żądane wartości `c` `d` i muszą być określone do generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="71eb3-522">Można oczekiwać, aby trafić `{controller}/{action}/{id?}`ten problem z domyślną trasą .</span><span class="sxs-lookup"><span data-stu-id="71eb3-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="71eb3-523">Ten problem jest rzadkością w praktyce, ponieważ `Url.Action` zawsze jawnie określa `controller` i `action` wartość.</span><span class="sxs-lookup"><span data-stu-id="71eb3-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="71eb3-524">Kilka przeciążeń [url.action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) podjąć obiekt wartości trasy, aby `controller` podać `action`wartości dla parametrów trasy innych niż i .</span><span class="sxs-lookup"><span data-stu-id="71eb3-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="71eb3-525">Obiekt wartości trasy jest często `id`używany z programem .</span><span class="sxs-lookup"><span data-stu-id="71eb3-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="71eb3-526">Na przykład `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="71eb3-527">Obiekt wartości trasy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-527">The route values object:</span></span>

* <span data-ttu-id="71eb3-528">Zgodnie z konwencją jest zwykle obiektem typu anonimowego.</span><span class="sxs-lookup"><span data-stu-id="71eb3-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="71eb3-529">Może być `IDictionary<>` POCO lub [POCO).](https://wikipedia.org/wiki/Plain_old_CLR_object)</span><span class="sxs-lookup"><span data-stu-id="71eb3-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="71eb3-530">Wszelkie dodatkowe wartości trasy, które nie pasują do parametrów trasy są umieszczane w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="71eb3-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="71eb3-531">Poprzedni kod generuje `/Products/Buy/17?color=red`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="71eb3-532">Poniższy kod generuje bezwzględny adres URL:</span><span class="sxs-lookup"><span data-stu-id="71eb3-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="71eb3-533">Aby utworzyć bezwzględny adres URL, użyj jednej z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="71eb3-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="71eb3-534">Przeciążenie, które `protocol`akceptuje .</span><span class="sxs-lookup"><span data-stu-id="71eb3-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="71eb3-535">Na przykład poprzedni kod.</span><span class="sxs-lookup"><span data-stu-id="71eb3-535">For example, the preceding code.</span></span>
* <span data-ttu-id="71eb3-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), który domyślnie generuje bezwzględne identyfikatory URI.</span><span class="sxs-lookup"><span data-stu-id="71eb3-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="71eb3-537">Generowanie adresów URL według trasy</span><span class="sxs-lookup"><span data-stu-id="71eb3-537">Generate URLs by route</span></span>

<span data-ttu-id="71eb3-538">Poprzedni kod zademonstrował wygenerowanie adresu URL przez przekazanie w kontrolerze i nazwie akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="71eb3-539">`IUrlHelper`udostępnia również rodzinę metod [Url.RouteUrl.](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*)</span><span class="sxs-lookup"><span data-stu-id="71eb3-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="71eb3-540">Metody te są podobne do [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nie `action` `controller` kopiują bieżących wartości i do wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="71eb3-541">Najczęstszym zastosowaniem `Url.RouteUrl`:</span><span class="sxs-lookup"><span data-stu-id="71eb3-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="71eb3-542">Określa nazwę trasy do wygenerowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="71eb3-543">Ogólnie nie określa nazwy kontrolera lub akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="71eb3-544">Następujący plik Razor generuje łącze `Destination_Route`HTML do:</span><span class="sxs-lookup"><span data-stu-id="71eb3-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="71eb3-545">Generowanie adresów URL w formacie HTML i Razor</span><span class="sxs-lookup"><span data-stu-id="71eb3-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="71eb3-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>zapewnia <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) i [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) do generowania `<form>` i `<a>` elementów odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="71eb3-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="71eb3-547">Te metody używają [metody Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) do generowania adresu URL i akceptują podobne argumenty.</span><span class="sxs-lookup"><span data-stu-id="71eb3-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="71eb3-548">Towarzysze `Url.RouteUrl` są `HtmlHelper` `Html.BeginRouteForm` i `Html.RouteLink` które mają podobną funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="71eb3-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="71eb3-549">TagHelpers generowania adresów URL za pośrednictwem `form` TagHelper i `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="71eb3-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="71eb3-550">Oba te `IUrlHelper` zastosowania do ich realizacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="71eb3-551">Aby uzyskać więcej [informacji, zobacz Pomocnicy tagów w formularzach.](xref:mvc/views/working-with-forms)</span><span class="sxs-lookup"><span data-stu-id="71eb3-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="71eb3-552">Widoki wewnątrz `IUrlHelper` jest dostępny `Url` za pośrednictwem właściwości dla każdego generowania adresów URL ad hoc nie objęte powyższe.</span><span class="sxs-lookup"><span data-stu-id="71eb3-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="71eb3-553">Generowanie adresu URL w wynikach działania</span><span class="sxs-lookup"><span data-stu-id="71eb3-553">URL generation in Action Results</span></span>

<span data-ttu-id="71eb3-554">Powyższe przykłady pokazano `IUrlHelper` przy użyciu w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="71eb3-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="71eb3-555">Najczęstszym użyciem w kontrolerze jest generowanie adresu URL jako część wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="71eb3-556">Klasy <xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> i klasy podstawowe zapewniają wygodę metod wyników akcji, które odwołują się do innej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="71eb3-557">Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika:</span><span class="sxs-lookup"><span data-stu-id="71eb3-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="71eb3-558">Wyniki akcji metody fabryczne, takie jak <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> i <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> `IUrlHelper`postępuj zgodnie z podobnym wzorcem do metod na .</span><span class="sxs-lookup"><span data-stu-id="71eb3-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="71eb3-559">Specjalny przypadek dla dedykowanych tras konwencjonalnych</span><span class="sxs-lookup"><span data-stu-id="71eb3-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="71eb3-560">[Routing konwencjonalny](#cr) może używać specjalnego rodzaju definicji trasy zwanej [dedykowaną trasą konwencjonalną.](#dcr)</span><span class="sxs-lookup"><span data-stu-id="71eb3-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="71eb3-561">W poniższym przykładzie `blog` nazwana trasa jest dedykowaną trasą konwencjonalną:</span><span class="sxs-lookup"><span data-stu-id="71eb3-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="71eb3-562">Korzystając z poprzednich definicji `Url.Action("Index", "Home")` trasy, generuje `/` ścieżkę adresu URL przy użyciu `default` trasy, ale dlaczego?</span><span class="sxs-lookup"><span data-stu-id="71eb3-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="71eb3-563">Można się domyślać, że wartości `{ controller = Home, action = Index }` trasy `blog`wystarczą do wygenerowania adresu URL za pomocą , a wynikiem będzie `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="71eb3-564">[Dedykowane konwencjonalne trasy](#dcr) opierają się na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiedniego parametru trasy, który uniemożliwia zbyt [chciwość](xref:fundamentals/routing#greedy) trasy z generowaniem adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="71eb3-565">W tym przypadku wartościami domyślnymi są `{ controller = Blog, action = Article }`, i ani `controller` ani nie `action` pojawia się jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="71eb3-566">Podczas generowania adresu URL podczas routingu podane wartości muszą być zgodne z wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="71eb3-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="71eb3-567">Generowanie `blog` adresu URL `{ controller = Home, action = Index }` za pomocą `{ controller = Blog, action = Article }`nie powiedzie się, ponieważ wartości nie są zgodne .</span><span class="sxs-lookup"><span data-stu-id="71eb3-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="71eb3-568">Routing następnie spada `default`z powrotem, aby spróbować , co powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="71eb3-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="71eb3-569">Obszary</span><span class="sxs-lookup"><span data-stu-id="71eb3-569">Areas</span></span>

<span data-ttu-id="71eb3-570">[Obszary](xref:mvc/controllers/areas) są funkcją MVC używaną do organizowania powiązanych funkcji w grupę jako osobną:</span><span class="sxs-lookup"><span data-stu-id="71eb3-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="71eb3-571">Routing obszaru nazw dla akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="71eb3-572">Struktura folderów dla widoków.</span><span class="sxs-lookup"><span data-stu-id="71eb3-572">Folder structure for views.</span></span>

<span data-ttu-id="71eb3-573">Korzystanie z obszarów umożliwia aplikacji wiele kontrolerów o tej samej nazwie, o ile mają różne obszary.</span><span class="sxs-lookup"><span data-stu-id="71eb3-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="71eb3-574">Za pomocą obszarów tworzy hierarchię do celów routingu, dodając inny parametr trasy, `area` do `controller` i `action`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="71eb3-575">W tej sekcji omówiono sposób interakcji routingu z obszarami.</span><span class="sxs-lookup"><span data-stu-id="71eb3-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="71eb3-576">Szczegółowe informacje na temat sposobu wykorzystania obszarów z widokami można znaleźć w części [Obszary.](xref:mvc/controllers/areas)</span><span class="sxs-lookup"><span data-stu-id="71eb3-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="71eb3-577">Poniższy przykład konfiguruje MVC do używania domyślnej trasy konwencjonalnej `area` i trasy dla nazwanej: `area` `Blog`</span><span class="sxs-lookup"><span data-stu-id="71eb3-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="71eb3-578">W poprzednim kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> jest wywoływana `"blog_route"`w celu utworzenia pliku .</span><span class="sxs-lookup"><span data-stu-id="71eb3-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="71eb3-579">Drugi parametr `"Blog"`, to nazwa obszaru.</span><span class="sxs-lookup"><span data-stu-id="71eb3-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="71eb3-580">Podczas dopasowywania `/Manage/Users/AddUser`ścieżki `"blog_route"` ADRESU URL, `{ area = Blog, controller = Users, action = AddUser }`takiej jak, trasa generuje wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="71eb3-581">Wartość `area` trasy jest wytwarzana przez `area`wartość domyślną dla .</span><span class="sxs-lookup"><span data-stu-id="71eb3-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="71eb3-582">Trasa utworzona `MapAreaControllerRoute` przez jest równoważna następującej trasie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="71eb3-583">`MapAreaControllerRoute`tworzy trasę przy użyciu zarówno wartości `area` domyślnej, jak i `Blog`ograniczenia dla używania podanej nazwy obszaru, w tym przypadku .</span><span class="sxs-lookup"><span data-stu-id="71eb3-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="71eb3-584">Wartość domyślna zapewnia, że trasa `{ area = Blog, ... }`zawsze tworzy, `{ area = Blog, ... }` ograniczenie wymaga wartości generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="71eb3-585">Routing konwencjonalny jest zależny od zamówienia.</span><span class="sxs-lookup"><span data-stu-id="71eb3-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="71eb3-586">Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej, ponieważ są bardziej szczegółowe niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="71eb3-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="71eb3-587">W poprzednim przykładzie wartości `{ area = Blog, controller = Users, action = AddUser }` trasy są zgodne z następującą czynnością:</span><span class="sxs-lookup"><span data-stu-id="71eb3-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="71eb3-588">Atrybut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) oznacza kontroler jako część obszaru.</span><span class="sxs-lookup"><span data-stu-id="71eb3-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="71eb3-589">Ten kontroler znajduje `Blog` się w obszarze.</span><span class="sxs-lookup"><span data-stu-id="71eb3-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="71eb3-590">Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru `area` i **nie** są zgodne, gdy wartość trasy jest dostarczana przez routing.</span><span class="sxs-lookup"><span data-stu-id="71eb3-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="71eb3-591">W poniższym przykładzie tylko pierwszy kontroler na `{ area = Blog, controller = Users, action = AddUser }`liście może być zgodny z wartościami trasy .</span><span class="sxs-lookup"><span data-stu-id="71eb3-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="71eb3-592">Obszar nazw każdego kontrolera jest pokazany w tym miejscu dla kompletności.</span><span class="sxs-lookup"><span data-stu-id="71eb3-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="71eb3-593">Jeśli poprzednie kontrolery używają tego samego obszaru nazw, zostanie wygenerowany błąd kompilatora.</span><span class="sxs-lookup"><span data-stu-id="71eb3-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="71eb3-594">Przestrzenie nazw klas nie mają wpływu na routing MVC.</span><span class="sxs-lookup"><span data-stu-id="71eb3-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="71eb3-595">Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko `area` wtedy, gdy ich nazwa obszaru jest podana przez wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="71eb3-596">Trzeci kontroler nie jest członkiem dowolnego obszaru i może być `area` dopasowany tylko wtedy, gdy żadna wartość nie jest dostarczana przez routing.</span><span class="sxs-lookup"><span data-stu-id="71eb3-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="71eb3-597">Pod względem dopasowania *bez wartości,* `area` brak wartości jest taki sam, jak gdyby wartość dla `area` były null lub pusty ciąg.</span><span class="sxs-lookup"><span data-stu-id="71eb3-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="71eb3-598">Podczas wykonywania akcji wewnątrz obszaru, wartość `area` trasy jest dostępna jako [wartość otoczenia](#ambient) dla routingu do użycia do generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="71eb3-599">Oznacza to, że domyślnie obszary działają *lepkie* dla generowania adresów URL, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="71eb3-600">Poniższy kod generuje adres `/Zebra/Users/AddUser`URL do:</span><span class="sxs-lookup"><span data-stu-id="71eb3-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="71eb3-601">Definicja akcji</span><span class="sxs-lookup"><span data-stu-id="71eb3-601">Action definition</span></span>

<span data-ttu-id="71eb3-602">Metody publiczne na kontrolerze, z wyjątkiem tych z atrybutem [NonAction,](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) są akcje.</span><span class="sxs-lookup"><span data-stu-id="71eb3-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="71eb3-603">Przykładowy kod</span><span class="sxs-lookup"><span data-stu-id="71eb3-603">Sample code</span></span>

 * <span data-ttu-id="71eb3-604">[Metoda MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) jest uwzględniona w [przykładowym pobieraniu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) i jest używana do wyświetlania informacji o routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="71eb3-605">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71eb3-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71eb3-606">ASP.NET Core MVC używa [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) Routing do dopasowania adresów URL przychodzących żądań i mapowania ich na akcje.</span><span class="sxs-lookup"><span data-stu-id="71eb3-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="71eb3-607">Trasy są definiowane w kodzie startowym lub atrybutach.</span><span class="sxs-lookup"><span data-stu-id="71eb3-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="71eb3-608">Trasy opisują, jak ścieżki adresów URL powinny być dopasowywać do akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="71eb3-609">Trasy są również używane do generowania adresów URL (dla linków) wysyłanych w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="71eb3-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="71eb3-610">Akcje są kierowane konwencjonalnie lub są kierowane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="71eb3-611">Umieszczenie trasy na kontrolerze lub akcji sprawia, że atrybut jest kierowany.</span><span class="sxs-lookup"><span data-stu-id="71eb3-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="71eb3-612">Zobacz [Routing mieszany, aby](#routing-mixed-ref-label) uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="71eb3-613">W tym dokumencie opisano interakcje między MVC i routingu i jak typowe aplikacje MVC korzystać z funkcji routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="71eb3-614">Zobacz [Routing, aby](xref:fundamentals/routing) uzyskać szczegółowe informacje na temat routingu zaawansowanego.</span><span class="sxs-lookup"><span data-stu-id="71eb3-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="71eb3-615">Konfigurowanie oprogramowania pośredniczącego routingu</span><span class="sxs-lookup"><span data-stu-id="71eb3-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="71eb3-616">W metodzie *Konfigurowanie* może być widoczny kod podobny do:</span><span class="sxs-lookup"><span data-stu-id="71eb3-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="71eb3-617">Wewnątrz wywołania `UseMvc` `MapRoute` do , służy do tworzenia jednej trasy, która `default` będziemy odnosić się do trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="71eb3-618">Większość aplikacji MVC użyje trasy z `default` szablonem podobnym do trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="71eb3-619">Szablon `"{controller=Home}/{action=Index}/{id?}"` trasy może odpowiadać `/Products/Details/5` ścieżce adresu URL, takiej jak i wyodrębni wartości `{ controller = Products, action = Details, id = 5 }` trasy przez tokenizację ścieżki.</span><span class="sxs-lookup"><span data-stu-id="71eb3-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="71eb3-620">MVC spróbuje zlokalizować `ProductsController` kontroler o `Details`nazwie i uruchomić akcję:</span><span class="sxs-lookup"><span data-stu-id="71eb3-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="71eb3-621">Należy zauważyć, że w tym przykładzie `id = 5` powiązanie `id` modelu `5` będzie używać wartości, aby ustawić parametr podczas wywoływania tej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="71eb3-622">Zobacz [powiązanie modelu, aby](../models/model-binding.md) uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="71eb3-623">Korzystanie `default` z trasy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="71eb3-624">Szablon trasy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-624">The route template:</span></span>

* <span data-ttu-id="71eb3-625">`{controller=Home}`definiuje `Home` jako domyślny`controller`</span><span class="sxs-lookup"><span data-stu-id="71eb3-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="71eb3-626">`{action=Index}`definiuje `Index` jako domyślny`action`</span><span class="sxs-lookup"><span data-stu-id="71eb3-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="71eb3-627">`{id?}`definiuje `id` jako opcjonalne</span><span class="sxs-lookup"><span data-stu-id="71eb3-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="71eb3-628">Domyślne i opcjonalne parametry trasy nie muszą znajdować się w ścieżce adresu URL dopasowania.</span><span class="sxs-lookup"><span data-stu-id="71eb3-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="71eb3-629">Zobacz [Odwołanie do szablonu trasy,](xref:fundamentals/routing#route-template-reference) aby uzyskać szczegółowy opis składni szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="71eb3-630">`"{controller=Home}/{action=Index}/{id?}"`może odpowiadać `/` ścieżce adresu URL `{ controller = Home, action = Index }`i będzie tworzyć wartości trasy .</span><span class="sxs-lookup"><span data-stu-id="71eb3-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="71eb3-631">Wartości i `controller` `action` używać wartości domyślnych, `id` nie daje wartość, ponieważ nie ma odpowiedniego segmentu w ścieżce adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="71eb3-632">MVC użyje tych wartości `HomeController` trasy, aby wybrać i `Index` akcji:</span><span class="sxs-lookup"><span data-stu-id="71eb3-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="71eb3-633">Przy użyciu tej definicji `HomeController.Index` kontrolera i szablonu trasy akcja zostanie wykonana dla dowolnej z następujących ścieżek adresu URL:</span><span class="sxs-lookup"><span data-stu-id="71eb3-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="71eb3-634">Metoda `UseMvcWithDefaultRoute`wygody:</span><span class="sxs-lookup"><span data-stu-id="71eb3-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="71eb3-635">Może być stosowany do wymiany:</span><span class="sxs-lookup"><span data-stu-id="71eb3-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="71eb3-636">`UseMvc`i `UseMvcWithDefaultRoute` dodaj wystąpienie `RouterMiddleware` do potoku oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="71eb3-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="71eb3-637">MVC nie wchodzi w interakcje bezpośrednio z oprogramowaniem pośredniczącym i używa routingu do obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="71eb3-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="71eb3-638">MVC jest połączony z trasami `MvcRouteHandler`za pośrednictwem wystąpienia .</span><span class="sxs-lookup"><span data-stu-id="71eb3-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="71eb3-639">Kod wewnątrz `UseMvc` jest podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="71eb3-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="71eb3-640">`UseMvc`nie definiuje bezpośrednio żadnych tras, dodaje symbol zastępczy do `attribute` kolekcji trasy dla trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="71eb3-641">Przeciążenie `UseMvc(Action<IRouteBuilder>)` umożliwia dodawanie własnych tras, a także obsługuje routing atrybutów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="71eb3-642">`UseMvc`a wszystkie jego odmiany dodają symbol zastępczy dla trasy atrybutu — routing atrybutów jest zawsze dostępny niezależnie od konfiguracji `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="71eb3-643">`UseMvcWithDefaultRoute`definiuje trasę domyślną i obsługuje routing atrybutów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="71eb3-644">Sekcja [Routing atrybutów](#attribute-routing-ref-label) zawiera więcej szczegółów dotyczących routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="71eb3-645">Routing konwencjonalny</span><span class="sxs-lookup"><span data-stu-id="71eb3-645">Conventional routing</span></span>

<span data-ttu-id="71eb3-646">Trasa: `default`</span><span class="sxs-lookup"><span data-stu-id="71eb3-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="71eb3-647">Poprzedni kod jest przykładem routingu konwencjonalnego.</span><span class="sxs-lookup"><span data-stu-id="71eb3-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="71eb3-648">Ten styl jest nazywany routingiem konwencjonalnym, ponieważ ustanawia *konwencję* dla ścieżek adresów URL:</span><span class="sxs-lookup"><span data-stu-id="71eb3-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="71eb3-649">Pierwszy segment ścieżki jest mapowana na nazwę kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="71eb3-650">Drugi mapuje do nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-650">The second maps to the action name.</span></span>
* <span data-ttu-id="71eb3-651">Trzeci segment jest używany `id`do opcjonalnego .</span><span class="sxs-lookup"><span data-stu-id="71eb3-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="71eb3-652">`id`mapuje do jednostki modelu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="71eb3-653">Korzystając `default` z tej trasy, `/Products/List` ścieżka `ProductsController.List` adresu `/Blog/Article/17` URL `BlogController.Article`jest mapowana do akcji i mapuje na .</span><span class="sxs-lookup"><span data-stu-id="71eb3-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="71eb3-654">To mapowanie jest oparte **tylko** na nazwach kontrolera i akcji i nie jest oparte na przestrzeniach nazw, lokalizacjach plików źródłowych ani parametrach metody.</span><span class="sxs-lookup"><span data-stu-id="71eb3-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="71eb3-655">Korzystanie z routingu konwencjonalnego z trasą domyślną umożliwia szybkie tworzenie aplikacji bez konieczności tworzenia nowego wzorca adresu URL dla każdej zdefiniowanej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="71eb3-656">W przypadku aplikacji z akcjami stylu CRUD spójność adresów URL między kontrolerami może pomóc uprościć kod i uczynić interfejs użytkownika bardziej przewidywalnym.</span><span class="sxs-lookup"><span data-stu-id="71eb3-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="71eb3-657">Jest `id` zdefiniowany jako opcjonalny przez szablon trasy, co oznacza, że akcje można wykonać bez identyfikatora podanego jako część adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="71eb3-658">Zwykle to, `id` co się stanie, jeśli zostanie pominięty `0` w adresie URL jest to, że będzie `id == 0`on ustawiony na powiązanie modelu, a w rezultacie żadna jednostka nie zostanie znaleziona w dopasowywaniu bazy danych .</span><span class="sxs-lookup"><span data-stu-id="71eb3-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="71eb3-659">Routing atrybutów może dać szczegółową kontrolę, aby identyfikator wymagany dla niektórych akcji, a nie dla innych.</span><span class="sxs-lookup"><span data-stu-id="71eb3-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="71eb3-660">Zgodnie z konwencją dokumentacja `id` będzie zawierać parametry opcjonalne, takie jak wtedy, gdy mogą pojawić się w prawidłowym użyciu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="71eb3-661">Wiele tras</span><span class="sxs-lookup"><span data-stu-id="71eb3-661">Multiple routes</span></span>

<span data-ttu-id="71eb3-662">Można dodać wiele `UseMvc` tras wewnątrz, `MapRoute`dodając więcej połączeń do .</span><span class="sxs-lookup"><span data-stu-id="71eb3-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="71eb3-663">W ten sposób można zdefiniować wiele konwencji lub dodać konwencjonalne trasy, które są dedykowane do określonej akcji, takich jak:</span><span class="sxs-lookup"><span data-stu-id="71eb3-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="71eb3-664">Trasa `blog` jest tutaj *dedykowaną trasą konwencjonalną,* co oznacza, że korzysta z konwencjonalnego systemu wyznaczania tras, ale jest dedykowana konkretnej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="71eb3-665">Ponieważ `controller` `action` i nie pojawiają się w szablonie trasy jako parametry, mogą mieć tylko wartości domyślne, a zatem ta trasa będzie zawsze mapować do akcji `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="71eb3-666">Trasy w kolekcji trasy są uporządkowane i będą przetwarzane w kolejności, w jakiej są dodawane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="71eb3-667">Tak więc w `blog` tym przykładzie trasa `default` zostanie wypróbowana przed trasą.</span><span class="sxs-lookup"><span data-stu-id="71eb3-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="71eb3-668">*Dedykowane konwencjonalne trasy* często używają `{*article}` parametrów trasy typu **catch-all,** takich jak przechwytywanie pozostałej części ścieżki adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="71eb3-669">Może to sprawić, że trasa będzie "zbyt chciwy", co oznacza, że pasuje do adresów URL, które mają być dopasowane do innych tras.</span><span class="sxs-lookup"><span data-stu-id="71eb3-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="71eb3-670">Umieść "chciwe" trasy w dalszej części tabeli tras, aby rozwiązać ten problem.</span><span class="sxs-lookup"><span data-stu-id="71eb3-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="71eb3-671">Opcja rezerwowa</span><span class="sxs-lookup"><span data-stu-id="71eb3-671">Fallback</span></span>

<span data-ttu-id="71eb3-672">W ramach przetwarzania żądań MVC sprawdzi, czy wartości trasy mogą służyć do znajdowania kontrolera i akcji w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="71eb3-673">Jeśli wartości trasy nie pasują do akcji, trasa nie jest uważana za dopasowanie, a następna trasa zostanie wypróbowana.</span><span class="sxs-lookup"><span data-stu-id="71eb3-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="71eb3-674">Jest to nazywane *rezerwowym*i ma na celu uproszczenie przypadków, w których konwencjonalne trasy nakładają się na siebie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="71eb3-675">Rozróżnianie działań</span><span class="sxs-lookup"><span data-stu-id="71eb3-675">Disambiguating actions</span></span>

<span data-ttu-id="71eb3-676">Gdy dwie akcje są zgodne za pośrednictwem routingu, MVC musi rozróżniać, aby wybrać "najlepszego" kandydata lub zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="71eb3-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="71eb3-677">Przykład:</span><span class="sxs-lookup"><span data-stu-id="71eb3-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="71eb3-678">Ten kontroler definiuje dwie akcje, które `/Products/Edit/17` pasują `{ controller = Products, action = Edit, id = 17 }`do ścieżki adresu URL i danych trasy .</span><span class="sxs-lookup"><span data-stu-id="71eb3-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="71eb3-679">Jest to typowy wzorzec dla `Edit(int)` kontrolerów MVC, gdzie `Edit(int, Product)` pokazuje formularz do edycji produktu i przetwarza zaksięgowanego formularza.</span><span class="sxs-lookup"><span data-stu-id="71eb3-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="71eb3-680">Aby to możliwe MVC musiałby `Edit(int, Product)` wybrać, kiedy `POST` żądanie `Edit(int)` jest HTTP i gdy zlecenie HTTP jest cokolwiek innego.</span><span class="sxs-lookup"><span data-stu-id="71eb3-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="71eb3-681">( `HttpPostAttribute` `[HttpPost]` ) jest `IActionConstraint` implementacją, która pozwoli na działanie, które mają `POST`być wybrane tylko wtedy, gdy czasownik HTTP jest .</span><span class="sxs-lookup"><span data-stu-id="71eb3-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="71eb3-682">Obecność sprawia, `IActionConstraint` że `Edit(int, Product)` "lepiej" mecz `Edit(int)`niż `Edit(int, Product)` , więc będzie sądzony pierwszy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="71eb3-683">Będzie trzeba tylko napisać `IActionConstraint` implementacje niestandardowe w wyspecjalizowanych scenariuszach, ale `HttpPostAttribute` ważne jest, aby zrozumieć rolę atrybutów, takich jak — podobne atrybuty są zdefiniowane dla innych zleceń HTTP.</span><span class="sxs-lookup"><span data-stu-id="71eb3-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="71eb3-684">W konwencjonalnym routingu jest często dla akcji, aby użyć tej `show form -> submit form` samej nazwy akcji, gdy są one częścią przepływu pracy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="71eb3-685">Wygoda tego wzorca stanie się bardziej widoczne po przejrzeniu [zrozumienia IActionConstraint](#understanding-iactionconstraint) sekcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="71eb3-686">Jeśli wiele tras pasuje, a MVC nie może znaleźć "najlepszej" trasy, wrzuci . `AmbiguousActionException`</span><span class="sxs-lookup"><span data-stu-id="71eb3-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="71eb3-687">Nazwy tras</span><span class="sxs-lookup"><span data-stu-id="71eb3-687">Route names</span></span>

<span data-ttu-id="71eb3-688">Ciągi `"blog"` i `"default"` w poniższych przykładach są nazwy tras:</span><span class="sxs-lookup"><span data-stu-id="71eb3-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="71eb3-689">Nazwy trasy nadają trasie nazwę logiczną, dzięki czemu nazwana trasa może być używana do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="71eb3-690">Znacznie upraszcza to tworzenie adresów URL, gdy kolejność tras może skomplikować generowanie adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="71eb3-691">Nazwy tras muszą być unikatowe dla całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="71eb3-692">Nazwy tras nie mają wpływu na dopasowywanie adresów URL lub obsługę żądań; są one używane tylko do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="71eb3-693">[Routing](xref:fundamentals/routing) ma bardziej szczegółowe informacje na temat generowania adresów URL, w tym generowanie adresów URL w pomocnikach specyficznych dla MVC.</span><span class="sxs-lookup"><span data-stu-id="71eb3-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="71eb3-694">Routing atrybutów</span><span class="sxs-lookup"><span data-stu-id="71eb3-694">Attribute routing</span></span>

<span data-ttu-id="71eb3-695">Routing atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="71eb3-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="71eb3-696">W poniższym `app.UseMvc();` przykładzie jest `Configure` używany w metodzie i nie trasa jest przekazywana.</span><span class="sxs-lookup"><span data-stu-id="71eb3-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="71eb3-697">Będzie `HomeController` pasować do zestawu adresów URL `{controller=Home}/{action=Index}/{id?}` podobnych do tego, co będzie pasować do trasy domyślnej:</span><span class="sxs-lookup"><span data-stu-id="71eb3-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="71eb3-698">Akcja `HomeController.Index()` zostanie wykonana dla dowolnej ścieżki `/`adresu `/Home`URL `/Home/Index`, lub .</span><span class="sxs-lookup"><span data-stu-id="71eb3-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="71eb3-699">W tym przykładzie przedstawiono kluczową różnicę programowania między routingiem atrybutów a routingiem konwencjonalnym.</span><span class="sxs-lookup"><span data-stu-id="71eb3-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="71eb3-700">Routing atrybutów wymaga więcej danych wejściowych, aby określić trasę; konwencjonalna trasa domyślna obsługuje trasy bardziej zwięźle.</span><span class="sxs-lookup"><span data-stu-id="71eb3-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="71eb3-701">Jednak routing atrybutów umożliwia (i wymaga) precyzyjnej kontroli, które szablony tras mają zastosowanie do każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="71eb3-702">W celu routingu atrybutu nazwa kontrolera i nazwy akcji nie odgrywają **żadnej** roli, w której wybrana jest akcja.</span><span class="sxs-lookup"><span data-stu-id="71eb3-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="71eb3-703">W tym przykładzie będą zgodne te same adresy URL, jak w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-703">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="71eb3-704">Powyższe szablony tras nie definiują parametrów trasy dla `action`, `area`i `controller`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="71eb3-705">W rzeczywistości te parametry trasy nie są dozwolone w trasach atrybutów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="71eb3-706">Ponieważ szablon trasy jest już skojarzony z akcją, nie ma sensu analizować nazwy akcji z adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="71eb3-707">Routing atrybutów z atrybutami Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="71eb3-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="71eb3-708">Routing atrybutów może również `Http[Verb]` korzystać `HttpPostAttribute`z atrybutów, takich jak .</span><span class="sxs-lookup"><span data-stu-id="71eb3-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="71eb3-709">Wszystkie te atrybuty mogą akceptować szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="71eb3-710">W tym przykładzie przedstawiono dwie akcje pasujące do tego samego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="71eb3-710">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="71eb3-711">Dla ścieżki adresu `/products` `ProductsApi.ListProducts` URL, takiej jak akcja, `GET` zostanie `ProductsApi.CreateProduct` wykonana, gdy zlecenie HTTP `POST`jest i będzie wykonywane, gdy zlecenie HTTP jest .</span><span class="sxs-lookup"><span data-stu-id="71eb3-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="71eb3-712">Routing atrybutów jako pierwszy pasuje do adresu URL względem zestawu szablonów tras zdefiniowanych przez atrybuty trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="71eb3-713">Gdy szablon trasy `IActionConstraint` pasuje, ograniczenia są stosowane w celu określenia, które akcje mogą być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="71eb3-714">Podczas tworzenia interfejsu API REST, rzadko, że `[Route(...)]` należy użyć w metodzie akcji, jak akcja zaakceptuje wszystkie metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="71eb3-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="71eb3-715">Lepiej jest użyć bardziej szczegółowe, `Http*Verb*Attributes` aby być dokładne o tym, co obsługuje interfejs API.</span><span class="sxs-lookup"><span data-stu-id="71eb3-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="71eb3-716">Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i zlecenia HTTP są mapowane na określone operacje logiczne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="71eb3-717">Ponieważ trasa atrybutu ma zastosowanie do określonej akcji, łatwo jest wprowadzić parametry wymagane jako część definicji szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="71eb3-718">W tym `id` przykładzie jest wymagane jako część ścieżki adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="71eb3-719">Akcja `ProductsApi.GetProduct(int)` zostanie wykonana dla ścieżki `/products/3` ADRESU URL, takiej `/products`jak ścieżka URL, taka jak .</span><span class="sxs-lookup"><span data-stu-id="71eb3-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="71eb3-720">Zobacz [Routing,](xref:fundamentals/routing) aby uzyskać pełny opis szablonów tras i powiązanych opcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="71eb3-721">Nazwa trasy</span><span class="sxs-lookup"><span data-stu-id="71eb3-721">Route Name</span></span>

<span data-ttu-id="71eb3-722">Poniższy kod definiuje nazwę `Products_List` *trasy:*</span><span class="sxs-lookup"><span data-stu-id="71eb3-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="71eb3-723">Nazwy tras mogą być używane do generowania adresu URL na podstawie określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="71eb3-724">Nazwy tras nie mają wpływu na zachowanie dopasowywania adresów URL routingu i są używane tylko do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="71eb3-725">Nazwy tras muszą być unikatowe dla całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="71eb3-726">Kontrastuje to z konwencjonalną *trasą domyślną,* `id` która definiuje parametr jako opcjonalny (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="71eb3-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="71eb3-727">Ta możliwość precyzyjnego określania interfejsów API ma `/products` `/products/5` zalety, takie jak zezwalanie i wysyłanie do różnych akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="71eb3-728">Łączenie tras</span><span class="sxs-lookup"><span data-stu-id="71eb3-728">Combining routes</span></span>

<span data-ttu-id="71eb3-729">Aby routing atrybutów był mniej powtarzalny, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="71eb3-730">Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzane do szablonów tras w akcjach.</span><span class="sxs-lookup"><span data-stu-id="71eb3-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="71eb3-731">Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w routingu atrybutu używanego przez kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="71eb3-732">W tym przykładzie `/products` ścieżka `ProductsApi.ListProducts`adresu URL `/products/5` może `ProductsApi.GetProduct(int)`być zgodna, a ścieżka adresu URL może być zgodna .</span><span class="sxs-lookup"><span data-stu-id="71eb3-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="71eb3-733">Obie te akcje są `GET` zgodne tylko z `HttpGetAttribute`protokołem HTTP, ponieważ są oznaczone symbolem .</span><span class="sxs-lookup"><span data-stu-id="71eb3-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="71eb3-734">Szablony tras zastosowane do akcji, `/` `~/` które rozpoczynają się lub nie są łączone z szablonami tras zastosowanymi do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="71eb3-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="71eb3-735">W tym przykładzie jest zgodny z zestawem ścieżek URL podobnych do *trasy domyślnej*.</span><span class="sxs-lookup"><span data-stu-id="71eb3-735">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="71eb3-736">Zamawianie tras atrybutów</span><span class="sxs-lookup"><span data-stu-id="71eb3-736">Ordering attribute routes</span></span>

<span data-ttu-id="71eb3-737">W przeciwieństwie do konwencjonalnych tras, które są wykonywane w zdefiniowanej kolejności, routing atrybutów tworzy drzewo i dopasowuje wszystkie trasy jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="71eb3-738">To zachowuje się tak, jakby wpisy trasy zostały umieszczone w idealnym zamawianiu; najbardziej konkretne trasy mają szansę wykonać przed bardziej ogólnymi trasami.</span><span class="sxs-lookup"><span data-stu-id="71eb3-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="71eb3-739">Na przykład trasa `blog/search/{topic}` podobna jest bardziej `blog/{*article}`szczegółowa niż trasa, taka jak .</span><span class="sxs-lookup"><span data-stu-id="71eb3-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="71eb3-740">Logicznie rzecz `blog/search/{topic}` biorąc trasa "działa" jako pierwsza, domyślnie, ponieważ jest to jedyna rozsądna kolejność.</span><span class="sxs-lookup"><span data-stu-id="71eb3-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="71eb3-741">Przy użyciu konwencjonalnego routingu deweloper jest odpowiedzialny za umieszczanie tras w żądanej kolejności.</span><span class="sxs-lookup"><span data-stu-id="71eb3-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="71eb3-742">Trasy atrybutów można skonfigurować kolejność, przy użyciu `Order` właściwości wszystkich atrybutów trasy pod warunkiem struktury.</span><span class="sxs-lookup"><span data-stu-id="71eb3-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="71eb3-743">Trasy są przetwarzane zgodnie z rosnącym `Order` rodzajem właściwości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="71eb3-744">Domyślna kolejność `0`to .</span><span class="sxs-lookup"><span data-stu-id="71eb3-744">The default order is `0`.</span></span> <span data-ttu-id="71eb3-745">Ustawienie trasy `Order = -1` przy użyciu będzie działać przed trasami, które nie ustawiają zamówienia.</span><span class="sxs-lookup"><span data-stu-id="71eb3-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="71eb3-746">Ustawienie trasy `Order = 1` przy użyciu będzie działać po domyślnej kolejności trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="71eb3-747">Unikaj w `Order`zależności od .</span><span class="sxs-lookup"><span data-stu-id="71eb3-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="71eb3-748">Jeśli miejsce w adresie URL wymaga jawnych wartości zamówienia do prawidłowego rozsyłania, prawdopodobnie jest to również mylące dla klientów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="71eb3-749">Ogólnie rzecz biorąc routing atrybutów wybierze prawidłową trasę z dopasowaniem adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="71eb3-750">Jeśli domyślna kolejność używana do generowania adresów URL nie działa, użycie nazwy trasy `Order` jako zastąpienia jest zwykle prostsze niż stosowanie właściwości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="71eb3-751">Routing stron razor i routing kontrolera MVC współużytkują implementację.</span><span class="sxs-lookup"><span data-stu-id="71eb3-751">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="71eb3-752">Informacje o kolejności trasy w tematach Strony razor są dostępne w [konwencji trasy Razor Pages i konwencji aplikacji: Kolejność tras](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="71eb3-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="71eb3-753">Zastępowanie tokenów w szablonach tras ([kontroler], [akcja], [obszar])</span><span class="sxs-lookup"><span data-stu-id="71eb3-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="71eb3-754">Dla wygody trasy atrybutów obsługują *zastępowanie tokenów* przez`[`załączanie tokenu w nawiasach kwadratowych ( , `]`).</span><span class="sxs-lookup"><span data-stu-id="71eb3-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="71eb3-755">Tokeny `[action]`, `[area]`i `[controller]` są zastępowane wartościami nazwy akcji, nazwy obszaru i nazwy kontrolera z akcji, w której trasa jest zdefiniowana.</span><span class="sxs-lookup"><span data-stu-id="71eb3-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="71eb3-756">W poniższym przykładzie akcje są zgodne ze ścieżkami adresu URL zgodnie z opisem w komentarzach:</span><span class="sxs-lookup"><span data-stu-id="71eb3-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="71eb3-757">Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="71eb3-758">Powyższy przykład będzie zachowywać się tak samo jak następujący kod:</span><span class="sxs-lookup"><span data-stu-id="71eb3-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="71eb3-759">Trasy atrybutów można również łączyć z dziedziczeniem.</span><span class="sxs-lookup"><span data-stu-id="71eb3-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="71eb3-760">Jest to szczególnie potężne w połączeniu z wymianą tokenów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-760">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="71eb3-761">Zastępowanie tokenów ma również zastosowanie do nazw tras zdefiniowanych przez trasy atrybutów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="71eb3-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="71eb3-763">Aby dopasować dosłowny `[` ogranicznik zastępujący token lub `]`, ucieknij go, powtarzając znak (`[[` lub `]]`).</span><span class="sxs-lookup"><span data-stu-id="71eb3-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="71eb3-764">Dostosowywanie zastępowania tokenów za pomocą transformatora parametrów</span><span class="sxs-lookup"><span data-stu-id="71eb3-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="71eb3-765">Zastąpienie tokenu można dostosować za pomocą transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="71eb3-766">Transformator parametrów `IOutboundParameterTransformer` implementuje i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="71eb3-767">Na przykład transformator parametrów `SlugifyParameterTransformer` `SubscriptionManagement` niestandardowych `subscription-management`zmienia wartość trasy na .</span><span class="sxs-lookup"><span data-stu-id="71eb3-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="71eb3-768">Jest `RouteTokenTransformerConvention` to konwencja modelu aplikacji, która:</span><span class="sxs-lookup"><span data-stu-id="71eb3-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="71eb3-769">Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="71eb3-770">Dostosowuje wartości tokenu marszruty atrybutu podczas ich zastępowyjca.</span><span class="sxs-lookup"><span data-stu-id="71eb3-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="71eb3-771">Jest `RouteTokenTransformerConvention` zarejestrowany jako opcja `ConfigureServices`w .</span><span class="sxs-lookup"><span data-stu-id="71eb3-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
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

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="71eb3-772">Wiele tras</span><span class="sxs-lookup"><span data-stu-id="71eb3-772">Multiple Routes</span></span>

<span data-ttu-id="71eb3-773">Routing atrybutów obsługuje definiowanie wielu tras, które osiągają tę samą akcję.</span><span class="sxs-lookup"><span data-stu-id="71eb3-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="71eb3-774">Najczęstszym zastosowaniem tego jest naśladować zachowanie *domyślnej trasy konwencjonalnej,* jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="71eb3-775">Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każdy z nich będzie łączyć z każdym z atrybutów trasy w metodach akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="71eb3-776">Gdy wiele atrybutów `IActionConstraint`trasy (które implementują) są umieszczane na akcję, a następnie każde ograniczenie akcji łączy się z szablonem trasy z atrybutu, który go zdefiniował.</span><span class="sxs-lookup"><span data-stu-id="71eb3-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="71eb3-777">Podczas korzystania z wielu tras na akcje mogą wydawać się zaawansowane, lepiej zachować przestrzeń adresów URL aplikacji proste i dobrze zdefiniowane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="71eb3-778">Użyj wielu tras w akcjach tylko wtedy, gdy jest to potrzebne, na przykład do obsługi istniejących klientów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="71eb3-779">Określanie parametrów opcjonalnych trasy atrybutu, wartości domyślnych i ograniczeń</span><span class="sxs-lookup"><span data-stu-id="71eb3-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="71eb3-780">Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="71eb3-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="71eb3-781">Zobacz [Odwołanie do szablonu trasy,](xref:fundamentals/routing#route-template-reference) aby uzyskać szczegółowy opis składni szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="71eb3-782">Niestandardowe atrybuty trasy przy użyciu`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="71eb3-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="71eb3-783">Wszystkie atrybuty trasy podane w `[Route(...)]` `[HttpGet(...)]` ramach ( , `IRouteTemplateProvider` itp.) implementują interfejs.</span><span class="sxs-lookup"><span data-stu-id="71eb3-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="71eb3-784">MVC wyszukuje atrybuty na klasy kontrolera i metody akcji `IRouteTemplateProvider` podczas uruchamiania aplikacji i używa tych, które implementują do tworzenia początkowego zestawu tras.</span><span class="sxs-lookup"><span data-stu-id="71eb3-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="71eb3-785">Można zaimplementować, `IRouteTemplateProvider` aby zdefiniować własne atrybuty trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="71eb3-786">Każdy `IRouteTemplateProvider` z nich umożliwia zdefiniowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:</span><span class="sxs-lookup"><span data-stu-id="71eb3-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="71eb3-787">Atrybut z powyższego przykładu `Template` automatycznie `"api/[controller]"` ustawia `[MyApiController]` na kiedy jest stosowany.</span><span class="sxs-lookup"><span data-stu-id="71eb3-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="71eb3-788">Dostosowywanie tras atrybutów za pomocą modelu aplikacji</span><span class="sxs-lookup"><span data-stu-id="71eb3-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="71eb3-789">*Model aplikacji* jest modelem obiektowym utworzonym podczas uruchamiania ze wszystkimi metadanymi używanymi przez MVC do kierowania i wykonywania akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="71eb3-790">*Model aplikacji* zawiera wszystkie dane zebrane z atrybutów trasy (przez `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="71eb3-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="71eb3-791">Można napisać *konwencje,* aby zmodyfikować model aplikacji w czasie uruchamiania, aby dostosować zachowanie routingu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="71eb3-792">W tej sekcji przedstawiono prosty przykład dostosowywania routingu przy użyciu modelu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="71eb3-793">Routing mieszany: routing atrybutów a routing konwencjonalny</span><span class="sxs-lookup"><span data-stu-id="71eb3-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="71eb3-794">Aplikacje MVC można mieszać użycie konwencjonalnych routingu i routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="71eb3-795">Typowe jest używanie konwencjonalnych tras dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.</span><span class="sxs-lookup"><span data-stu-id="71eb3-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="71eb3-796">Akcje są kierowane konwencjonalnie lub są kierowane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="71eb3-797">Umieszczenie trasy na kontrolerze lub akcji sprawia, że atrybut jest kierowany.</span><span class="sxs-lookup"><span data-stu-id="71eb3-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="71eb3-798">Akcje definiujące trasy atrybutów nie mogą być osiągane za pośrednictwem tras konwencjonalnych i odwrotnie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="71eb3-799">**Każdy** atrybut trasy na kontrolerze sprawia, że wszystkie akcje w atrybucie kontrolera kierowane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="71eb3-800">To, co odróżnia dwa typy systemów routingu, to proces stosowany po dopasowaniu adresu URL do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="71eb3-801">W routingu konwencjonalnym wartości trasy z dopasowania są używane do wybierania akcji i kontrolera z tabeli odnośnych wszystkich konwencjonalnych akcji kierowanych.</span><span class="sxs-lookup"><span data-stu-id="71eb3-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="71eb3-802">W routingu atrybutów każdy szablon jest już skojarzony z akcją i nie jest potrzebne dalsze wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="71eb3-803">Segmenty złożone</span><span class="sxs-lookup"><span data-stu-id="71eb3-803">Complex segments</span></span>

<span data-ttu-id="71eb3-804">Złożone segmenty (na `[Route("/dog{token}cat")]`przykład ) są przetwarzane przez dopasowanie literałów od prawej do lewej w sposób niechciwy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="71eb3-805">Zobacz [kod źródłowy](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) opisu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="71eb3-806">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="71eb3-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="71eb3-807">Generowanie adresu URL</span><span class="sxs-lookup"><span data-stu-id="71eb3-807">URL Generation</span></span>

<span data-ttu-id="71eb3-808">Aplikacje MVC mogą używać funkcji generowania adresów URL routingu do generowania łączy adresów URL do akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="71eb3-809">Generowanie adresów URL eliminuje adresy URL hardcoding, dzięki czemu kod jest bardziej niezawodny i niezawodny.</span><span class="sxs-lookup"><span data-stu-id="71eb3-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="71eb3-810">W tej sekcji skupiono się na funkcjach generowania adresów URL dostarczonych przez MVC i będzie obejmować tylko podstawy działania generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="71eb3-811">Zobacz [Routing, aby](xref:fundamentals/routing) uzyskać szczegółowy opis generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="71eb3-812">Interfejs `IUrlHelper` jest podstawowym elementem infrastruktury między MVC i routingu do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="71eb3-813">Znajdziesz wystąpienie `IUrlHelper` dostępne za pośrednictwem `Url` właściwości w kontrolerach, widokach i składnikach widoku.</span><span class="sxs-lookup"><span data-stu-id="71eb3-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="71eb3-814">W tym przykładzie `IUrlHelper` interfejs jest `Controller.Url` używany za pośrednictwem właściwości do generowania adresu URL do innej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="71eb3-815">Jeśli aplikacja używa domyślnej trasy konwencjonalnej, `url` wartością zmiennej `/UrlGeneration/Destination`będzie ciąg ścieżki adresu URL .</span><span class="sxs-lookup"><span data-stu-id="71eb3-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="71eb3-816">Ta ścieżka adresu URL jest tworzona przez routing przez połączenie wartości trasy `Url.Action` z bieżącego żądania (wartości otoczenia) z wartościami przekazanymi do szablonu trasy i zastąpienie ich:</span><span class="sxs-lookup"><span data-stu-id="71eb3-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="71eb3-817">Każdy parametr trasy w szablonie trasy ma swoją wartość zastąpiona pasującymi nazwami z wartościami i wartościami otoczenia.</span><span class="sxs-lookup"><span data-stu-id="71eb3-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="71eb3-818">Parametr trasy, który nie ma wartości, może użyć wartości domyślnej, jeśli ją ma, lub zostać `id` pominięty, jeśli jest opcjonalny (jak w tym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="71eb3-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="71eb3-819">Generowanie adresu URL zakończy się niepowodzeniem, jeśli dowolny wymagany parametr trasy nie ma odpowiedniej wartości.</span><span class="sxs-lookup"><span data-stu-id="71eb3-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="71eb3-820">Jeśli generowanie adresu URL nie powiedzie się dla trasy, następna trasa jest wypróbowywych, dopóki nie zostanie wypróbowane wszystkie trasy lub zostanie znalezione dopasowanie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="71eb3-821">W powyższym `Url.Action` przykładzie przyjęto założenie, że routing konwencjonalny, ale generowanie adresów URL działa podobnie z routingiem atrybutów, chociaż pojęcia są różne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="71eb3-822">W przypadku routingu konwencjonalnego wartości trasy są używane do `controller` `action` rozwijania szablonu, a wartości trasy dla i zwykle pojawiają się w tym szablonie — działa to, ponieważ adresy URL dopasowane przez routing są zgodne z *konwencją*.</span><span class="sxs-lookup"><span data-stu-id="71eb3-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="71eb3-823">W routingu atrybutów wartości `controller` `action` trasy i nie mogą być wyświetlane w szablonie — zamiast tego są używane do wyszukiwania szablonu, którego chcesz użyć.</span><span class="sxs-lookup"><span data-stu-id="71eb3-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="71eb3-824">W tym przykładzie użyto routingu atrybutów:</span><span class="sxs-lookup"><span data-stu-id="71eb3-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="71eb3-825">MVC tworzy tabelę odnośną wszystkich akcji kierowanych `controller` `action` atrybutami i będzie pasować do wartości i wartości, aby wybrać szablon trasy do generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="71eb3-826">W powyższej `custom/url/to/destination` próbce jest generowany.</span><span class="sxs-lookup"><span data-stu-id="71eb3-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="71eb3-827">Generowanie adresów URL według nazwy akcji</span><span class="sxs-lookup"><span data-stu-id="71eb3-827">Generating URLs by action name</span></span>

<span data-ttu-id="71eb3-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="71eb3-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="71eb3-829">`Action`) i wszystkie powiązane przeciążenia są oparte na tym pomyśle, który chcesz określić, do czego łączysz, określając nazwę kontrolera i nazwę akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="71eb3-830">Podczas `Url.Action`korzystania z , `controller` bieżące wartości trasy `action` dla `controller` i `action` są określone dla Ciebie - wartość i są częścią wartości *otoczenia* **i** *wartości*.</span><span class="sxs-lookup"><span data-stu-id="71eb3-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="71eb3-831">Metoda `Url.Action`, zawsze używa bieżących `action` `controller` wartości i wygeneruje ścieżkę adresu URL, która kieruje do bieżącej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="71eb3-832">Routing próbuje użyć wartości w wartościach otoczenia, aby wypełnić informacje, które nie zostały podane podczas generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="71eb3-833">Przy użyciu `{a}/{b}/{c}/{d}` wartości `{ a = Alice, b = Bob, c = Carol, d = David }`podobnych i otaczających trasy routing ma wystarczającą ilość informacji, aby wygenerować adres URL bez żadnych dodatkowych wartości — ponieważ wszystkie parametry trasy mają wartość.</span><span class="sxs-lookup"><span data-stu-id="71eb3-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="71eb3-834">Jeśli wartość `{ d = Donovan }`zostanie dodana, wartość `{ d = David }` zostanie zignorowana, a `Alice/Bob/Carol/Donovan`wygenerowana ścieżka adresu URL będzie .</span><span class="sxs-lookup"><span data-stu-id="71eb3-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="71eb3-835">Ścieżki adresów URL są hierarchiczne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-835">URL paths are hierarchical.</span></span> <span data-ttu-id="71eb3-836">W powyższym przykładzie, jeśli `{ c = Cheryl }`dodano wartość, `{ c = Carol, d = David }` obie wartości zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="71eb3-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="71eb3-837">W takim przypadku nie mamy `d` już wartości dla i generowanie adresów URL zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="71eb3-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="71eb3-838">Należy określić żądaną wartość `c` `d`i .</span><span class="sxs-lookup"><span data-stu-id="71eb3-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="71eb3-839">Można oczekiwać, aby trafić ten`{controller}/{action}/{id?}`problem z domyślną trasą ( `Url.Action` ) - ale `controller` `action` rzadko można spotkać to zachowanie w praktyce, jak zawsze jawnie określić i wartość.</span><span class="sxs-lookup"><span data-stu-id="71eb3-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="71eb3-840">Dłuższe `Url.Action` przeciążenia również podjąć dodatkowe wartości *trasy* obiektu, `controller` aby `action`podać wartości dla parametrów trasy innych niż i .</span><span class="sxs-lookup"><span data-stu-id="71eb3-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="71eb3-841">Najczęściej widać to używane z `id` `Url.Action("Buy", "Products", new { id = 17 })`like .</span><span class="sxs-lookup"><span data-stu-id="71eb3-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="71eb3-842">Zgodnie z konwencją obiekt *wartości trasy* jest zwykle obiektem `IDictionary<>` typu anonimowego, ale może być również *zwykłym lub starym obiektem .NET.*</span><span class="sxs-lookup"><span data-stu-id="71eb3-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="71eb3-843">Wszelkie dodatkowe wartości trasy, które nie pasują do parametrów trasy są umieszczane w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="71eb3-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="71eb3-844">Aby utworzyć bezwzględny adres URL, użyj `protocol`przeciążenia, które akceptuje:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="71eb3-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="71eb3-845">Generowanie adresów URL według trasy</span><span class="sxs-lookup"><span data-stu-id="71eb3-845">Generating URLs by route</span></span>

<span data-ttu-id="71eb3-846">Powyższy kod wykazał generowanie adresu URL przez przekazanie w kontrolerze i nazwę akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="71eb3-847">`IUrlHelper`zapewnia również `Url.RouteUrl` rodzinę metod.</span><span class="sxs-lookup"><span data-stu-id="71eb3-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="71eb3-848">Metody te są `Url.Action`podobne do , ale nie `action` kopiują bieżące wartości i `controller` do wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="71eb3-849">Najczęstszym zastosowaniem jest określenie nazwy trasy, aby użyć określonej trasy do wygenerowania adresu URL, zazwyczaj *bez* określania nazwy kontrolera lub akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="71eb3-850">Generowanie adresów URL w formacie HTML</span><span class="sxs-lookup"><span data-stu-id="71eb3-850">Generating URLs in HTML</span></span>

<span data-ttu-id="71eb3-851">`IHtmlHelper``HtmlHelper` zawiera metody `Html.BeginForm` `Html.ActionLink` oraz `<form>` do `<a>` generowania i elementów odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="71eb3-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="71eb3-852">Metody te `Url.Action` używają metody do generowania adresu URL i akceptują podobne argumenty.</span><span class="sxs-lookup"><span data-stu-id="71eb3-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="71eb3-853">Towarzysze `Url.RouteUrl` są `HtmlHelper` `Html.BeginRouteForm` i `Html.RouteLink` które mają podobną funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="71eb3-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="71eb3-854">TagHelpers generowania adresów URL za pośrednictwem `form` TagHelper i `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="71eb3-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="71eb3-855">Oba te `IUrlHelper` zastosowania do ich realizacji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="71eb3-856">Aby uzyskać więcej informacji, zobacz [Praca z formularzami.](../views/working-with-forms.md)</span><span class="sxs-lookup"><span data-stu-id="71eb3-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="71eb3-857">Widoki wewnątrz `IUrlHelper` jest dostępny `Url` za pośrednictwem właściwości dla każdego generowania adresów URL ad hoc nie objęte powyższe.</span><span class="sxs-lookup"><span data-stu-id="71eb3-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="71eb3-858">Generowanie adresów URL w wynikach akcji</span><span class="sxs-lookup"><span data-stu-id="71eb3-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="71eb3-859">Powyższe przykłady zostały `IUrlHelper` pokazane przy użyciu w kontrolerze, podczas gdy najczęstszym użyciem w kontrolerze jest generowanie adresu URL jako część wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="71eb3-860">Klasy `ControllerBase` `Controller` i klasy podstawowe zapewniają wygodę metod wyników akcji, które odwołują się do innej akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="71eb3-861">Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="71eb3-861">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="71eb3-862">Metody fabryczne wyników akcji są zgodne `IUrlHelper`z podobnym wzorcem do metod na .</span><span class="sxs-lookup"><span data-stu-id="71eb3-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="71eb3-863">Specjalny przypadek dla dedykowanych tras konwencjonalnych</span><span class="sxs-lookup"><span data-stu-id="71eb3-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="71eb3-864">Routing konwencjonalny może używać specjalnego rodzaju definicji trasy zwanej *dedykowaną trasą konwencjonalną.*</span><span class="sxs-lookup"><span data-stu-id="71eb3-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="71eb3-865">W poniższym przykładzie `blog` nazwana trasa jest dedykowaną trasą konwencjonalną.</span><span class="sxs-lookup"><span data-stu-id="71eb3-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="71eb3-866">Korzystając z tych `Url.Action("Index", "Home")` definicji trasy, `/` wygeneruje ścieżkę adresu URL z trasą, `default` ale dlaczego?</span><span class="sxs-lookup"><span data-stu-id="71eb3-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="71eb3-867">Można się domyślać, że wartości `{ controller = Home, action = Index }` trasy `blog`wystarczą do wygenerowania adresu URL za pomocą , a wynikiem będzie `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="71eb3-868">Dedykowane konwencjonalne trasy opierają się na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiedniego parametru trasy, który uniemożliwia trasę jako "zbyt chciwy" z generowaniem adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="71eb3-869">W tym przypadku wartościami domyślnymi są `{ controller = Blog, action = Article }`, i ani `controller` ani nie `action` pojawia się jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="71eb3-870">Podczas generowania adresu URL podczas routingu podane wartości muszą być zgodne z wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="71eb3-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="71eb3-871">Generowanie `blog` adresów URL `{ controller = Home, action = Index }` za pomocą `{ controller = Blog, action = Article }`zakończy się niepowodzeniem, ponieważ wartości nie są zgodne .</span><span class="sxs-lookup"><span data-stu-id="71eb3-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="71eb3-872">Routing następnie spada `default`z powrotem, aby spróbować , co powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="71eb3-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="71eb3-873">Obszary</span><span class="sxs-lookup"><span data-stu-id="71eb3-873">Areas</span></span>

<span data-ttu-id="71eb3-874">[Obszary](areas.md) są funkcją MVC używaną do organizowania powiązanych funkcji w grupę jako oddzielny obszar nazw routingu (dla akcji kontrolera) i strukturę folderów (dla widoków).</span><span class="sxs-lookup"><span data-stu-id="71eb3-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="71eb3-875">Korzystanie z obszarów pozwala aplikacji mieć wiele kontrolerów o tej samej nazwie - tak długo, jak mają różne *obszary.*</span><span class="sxs-lookup"><span data-stu-id="71eb3-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="71eb3-876">Za pomocą obszarów tworzy hierarchię do celów routingu, dodając inny parametr trasy, `area` do `controller` i `action`.</span><span class="sxs-lookup"><span data-stu-id="71eb3-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="71eb3-877">W tej sekcji omówimy interakcje routingu z obszarami — zobacz [Obszary, aby](areas.md) uzyskać szczegółowe informacje o sposobie wykorzystania obszarów z widokami.</span><span class="sxs-lookup"><span data-stu-id="71eb3-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="71eb3-878">W poniższym przykładzie konfiguruje MVC użycie domyślnej trasy `Blog`konwencjonalnej i trasy *obszaru* dla obszaru o nazwie:</span><span class="sxs-lookup"><span data-stu-id="71eb3-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="71eb3-879">Podczas dopasowywania `/Manage/Users/AddUser`ścieżki adresu URL, takiej `{ area = Blog, controller = Users, action = AddUser }`jak, pierwsza trasa będzie tworzyć wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="71eb3-880">Wartość `area` trasy jest tworzona `area`przez wartość domyślną `MapAreaRoute` dla , w rzeczywistości trasa utworzona przez jest równoważna następującej:</span><span class="sxs-lookup"><span data-stu-id="71eb3-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="71eb3-881">`MapAreaRoute`tworzy trasę przy użyciu zarówno wartości `area` domyślnej, jak i `Blog`ograniczenia dla używania podanej nazwy obszaru, w tym przypadku .</span><span class="sxs-lookup"><span data-stu-id="71eb3-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="71eb3-882">Wartość domyślna zapewnia, że trasa `{ area = Blog, ... }`zawsze tworzy, `{ area = Blog, ... }` ograniczenie wymaga wartości generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="71eb3-883">Routing konwencjonalny jest zależny od zamówienia.</span><span class="sxs-lookup"><span data-stu-id="71eb3-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="71eb3-884">Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej w tabeli tras, ponieważ są bardziej szczegółowe niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="71eb3-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="71eb3-885">W powyższym przykładzie wartości trasy odpowiadałyby następującej akcji:</span><span class="sxs-lookup"><span data-stu-id="71eb3-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="71eb3-886">To, `AreaAttribute` co oznacza kontroler jako część obszaru, mówimy, że ten `Blog` kontroler jest w obszarze.</span><span class="sxs-lookup"><span data-stu-id="71eb3-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="71eb3-887">Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru `area` i **nie** będą zgodne, gdy wartość trasy jest dostarczana przez routing.</span><span class="sxs-lookup"><span data-stu-id="71eb3-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="71eb3-888">W poniższym przykładzie tylko pierwszy kontroler na `{ area = Blog, controller = Users, action = AddUser }`liście może być zgodny z wartościami trasy .</span><span class="sxs-lookup"><span data-stu-id="71eb3-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="71eb3-889">Obszar nazw każdego kontrolera jest wyświetlany w tym miejscu dla kompletności — w przeciwnym razie kontrolery będą miały konflikt nazewnictwa i wygenerować błąd kompilatora.</span><span class="sxs-lookup"><span data-stu-id="71eb3-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="71eb3-890">Przestrzenie nazw klas nie mają wpływu na routing MVC.</span><span class="sxs-lookup"><span data-stu-id="71eb3-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="71eb3-891">Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko `area` wtedy, gdy ich nazwa obszaru jest podana przez wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="71eb3-892">Trzeci kontroler nie jest członkiem dowolnego obszaru i może być `area` dopasowany tylko wtedy, gdy żadna wartość nie jest dostarczana przez routing.</span><span class="sxs-lookup"><span data-stu-id="71eb3-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="71eb3-893">Pod względem dopasowania *bez wartości,* `area` brak wartości jest taki sam, jak gdyby wartość dla `area` były null lub pusty ciąg.</span><span class="sxs-lookup"><span data-stu-id="71eb3-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="71eb3-894">Podczas wykonywania akcji wewnątrz obszaru, wartość `area` trasy dla będzie dostępna jako *wartość otoczenia* dla routingu do użycia do generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="71eb3-895">Oznacza to, że domyślnie obszary działają *lepkie* dla generowania adresów URL, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="71eb3-896">Opis IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="71eb3-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="71eb3-897">Ta sekcja jest głębokie zagłębienie na wewnętrznych framework i jak MVC wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="71eb3-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="71eb3-898">Typowa aplikacja nie będzie potrzebowała`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="71eb3-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="71eb3-899">Prawdopodobnie już używane, `IActionConstraint` nawet jeśli nie jesteś zaznajomiony z interfejsem.</span><span class="sxs-lookup"><span data-stu-id="71eb3-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="71eb3-900">Atrybut `[HttpGet]` i podobne `[Http-VERB]` atrybuty implementują `IActionConstraint` w celu ograniczenia wykonywania metody akcji.</span><span class="sxs-lookup"><span data-stu-id="71eb3-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="71eb3-901">Przy założeniu domyślnej trasy `/Products/Edit` konwencjonalnej `{ controller = Products, action = Edit }`ścieżka adresu URL będzie tworzyć wartości, które pasują do **obu** akcji pokazanych w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="71eb3-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="71eb3-902">W `IActionConstraint` terminologii chcielibyśmy powiedzieć, że oba te działania są uważane za kandydatów - ponieważ oba pasują do danych trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="71eb3-903">Gdy `HttpGetAttribute` wykonuje, powie, że *Edit()* jest dopasowanie dla *GET* i nie jest zgodne dla innych zleceń HTTP.</span><span class="sxs-lookup"><span data-stu-id="71eb3-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="71eb3-904">Akcja `Edit(...)` nie ma żadnych ograniczeń zdefiniowanych, a więc będzie pasować do dowolnego zlecenia HTTP.</span><span class="sxs-lookup"><span data-stu-id="71eb3-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="71eb3-905">Więc zakładając `POST` - `Edit(...)` tylko mecze.</span><span class="sxs-lookup"><span data-stu-id="71eb3-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="71eb3-906">Ale dla `GET` obu działań może jeszcze dopasować `IActionConstraint` - jednak akcja z akcją jest zawsze uważana za *lepszą* niż akcja bez.</span><span class="sxs-lookup"><span data-stu-id="71eb3-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="71eb3-907">`Edit()` Dlatego, `[HttpGet]` że jest uważany za bardziej szczegółowe i zostanie wybrany, jeśli obie akcje mogą być zgodne.</span><span class="sxs-lookup"><span data-stu-id="71eb3-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="71eb3-908">Koncepcyjnie `IActionConstraint` jest formą *przeciążenia*, ale zamiast przeciążać metody o tej samej nazwie, jest przeciążenie między akcjami, które pasują do tego samego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="71eb3-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="71eb3-909">Routing atrybutów również `IActionConstraint` używa i może spowodować akcje z różnych kontrolerów zarówno uważane za kandydatów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="71eb3-910">Wdrażanie IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="71eb3-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="71eb3-911">Najprostszym `IActionConstraint` sposobem zaimplementowania jest utworzenie `System.Attribute` klasy pochodzące z i umieścić go na akcje i kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="71eb3-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="71eb3-912">MVC automatycznie odnajduje wszystkie, `IActionConstraint` które są stosowane jako atrybuty.</span><span class="sxs-lookup"><span data-stu-id="71eb3-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="71eb3-913">Model aplikacji służy do stosowania ograniczeń i jest to prawdopodobnie najbardziej elastyczne podejście, ponieważ umożliwia metaprogramowanie sposobu ich stosowania.</span><span class="sxs-lookup"><span data-stu-id="71eb3-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="71eb3-914">W poniższym przykładzie ograniczenie wybiera akcję na podstawie *kodu kraju* z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="71eb3-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="71eb3-915">[Pełna próbka na GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="71eb3-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="71eb3-916">Użytkownik jest odpowiedzialny za `Accept` wdrożenie metody i wybranie "Order" dla ograniczenia do wykonania.</span><span class="sxs-lookup"><span data-stu-id="71eb3-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="71eb3-917">W takim przypadku `Accept` metoda `true` powraca do oznaczenia akcji `country` jest dopasowanie, gdy wartość trasy pasuje.</span><span class="sxs-lookup"><span data-stu-id="71eb3-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="71eb3-918">To różni się `RouteValueAttribute` od tego, że umożliwia powrót do akcji niepodana.</span><span class="sxs-lookup"><span data-stu-id="71eb3-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="71eb3-919">Przykład pokazuje, że jeśli `en-US` zdefiniujesz `fr-FR` akcję, kod kraju, taki jak powróci `[CountrySpecific(...)]` do bardziej ogólnego kontrolera, który nie został zastosowany.</span><span class="sxs-lookup"><span data-stu-id="71eb3-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="71eb3-920">Właściwość `Order` decyduje, który *etap* ograniczenia jest częścią.</span><span class="sxs-lookup"><span data-stu-id="71eb3-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="71eb3-921">Ograniczenia akcji są uruchamiane `Order`w grupach na podstawie pliku .</span><span class="sxs-lookup"><span data-stu-id="71eb3-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="71eb3-922">Na przykład wszystkie atrybuty metody http dostarczone `Order` przez platformę HTTP używają tej samej wartości, dzięki czemu są uruchamiane na tym samym etapie.</span><span class="sxs-lookup"><span data-stu-id="71eb3-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="71eb3-923">Możesz mieć tyle etapów, ile potrzeba do wdrożenia żądanych zasad.</span><span class="sxs-lookup"><span data-stu-id="71eb3-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="71eb3-924">Aby zdecydować o `Order` wartości, aby zastanowić się, czy ograniczenie powinno być stosowane przed metodami HTTP.</span><span class="sxs-lookup"><span data-stu-id="71eb3-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="71eb3-925">Najpierw działają niższe liczby.</span><span class="sxs-lookup"><span data-stu-id="71eb3-925">Lower numbers run first.</span></span>

::: moniker-end
