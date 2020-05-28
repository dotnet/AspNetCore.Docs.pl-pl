---
<span data-ttu-id="2fa93-101">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-102">'Blazor'</span></span>
- <span data-ttu-id="2fa93-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-103">'Identity'</span></span>
- <span data-ttu-id="2fa93-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-105">'Razor'</span></span>
- <span data-ttu-id="2fa93-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="2fa93-107">Routing do akcji kontrolera w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fa93-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="2fa93-108">[Ryan Nowak](https://github.com/rynowak), [Kirka Larkin](https://twitter.com/serpent5)i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2fa93-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2fa93-109">Kontrolery ASP.NET Core używają [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) routingu do dopasowywania adresów URL żądań przychodzących i mapują je na [Akcje](#action).</span><span class="sxs-lookup"><span data-stu-id="2fa93-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="2fa93-110">Szablony tras:</span><span class="sxs-lookup"><span data-stu-id="2fa93-110">Routes templates:</span></span>

* <span data-ttu-id="2fa93-111">Są zdefiniowane w kodzie startowym lub atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="2fa93-112">Opisz, w jaki sposób ścieżki URL są dopasowywane do [akcji](#action).</span><span class="sxs-lookup"><span data-stu-id="2fa93-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="2fa93-113">Służy do generowania adresów URL dla łączy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="2fa93-114">Wygenerowane linki są zwykle zwracane w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="2fa93-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="2fa93-115">Akcje są albo [podkierowane do Konwencji](#cr) lub [kierowane przez atrybut](#ar).</span><span class="sxs-lookup"><span data-stu-id="2fa93-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="2fa93-116">Umieszczenie trasy na kontrolerze lub [akcji](#action) sprawia, że jest ona kierowana przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="2fa93-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="2fa93-117">Aby uzyskać więcej informacji, zobacz [Routing mieszany](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="2fa93-118">Ten dokument:</span><span class="sxs-lookup"><span data-stu-id="2fa93-118">This document:</span></span>

* <span data-ttu-id="2fa93-119">Wyjaśnia interakcje między MVC i Routing:</span><span class="sxs-lookup"><span data-stu-id="2fa93-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="2fa93-120">Jak typowe aplikacje MVC używają funkcji routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="2fa93-121">Obejmuje zarówno:</span><span class="sxs-lookup"><span data-stu-id="2fa93-121">Covers both:</span></span>
    * <span data-ttu-id="2fa93-122">[UNC routingu](#cr) zwykle używany z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="2fa93-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="2fa93-123">*Routing atrybutów* używany z interfejsami API REST.</span><span class="sxs-lookup"><span data-stu-id="2fa93-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="2fa93-124">Jeśli jesteś głównie zainteresowani routingiem interfejsów API REST, przejdź do sekcji [Routing atrybutów dla interfejsów API REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="2fa93-125">Szczegóły dotyczące routingu zaawansowanego znajdują się w temacie [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="2fa93-126">Odnosi się do domyślnego systemu routingu dodanego w ASP.NET Core 3,0, nazywanego routingiem punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="2fa93-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="2fa93-127">Możliwe jest używanie kontrolerów z poprzednią wersją routingu w celu zapewnienia zgodności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="2fa93-128">Instrukcje można znaleźć w [przewodniku migracji 2.2-3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="2fa93-129">Zapoznaj się z [wersją 2,2 tego dokumentu](xref:mvc/controllers/routing?view=aspnetcore-2.2) dotyczącą materiałów referencyjnych w starszym systemie routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="2fa93-130">Konfigurowanie trasy konwencjonalnej</span><span class="sxs-lookup"><span data-stu-id="2fa93-130">Set up conventional route</span></span>

<span data-ttu-id="2fa93-131">`Startup.Configure`w przypadku korzystania z [konwencjonalnego routingu](#crd)zazwyczaj ma kod podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="2fa93-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="2fa93-132">Wewnątrz wywołania do <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> , służy <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> do tworzenia pojedynczej trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="2fa93-133">Pojedyncza trasa ma nazwę `default` Route.</span><span class="sxs-lookup"><span data-stu-id="2fa93-133">The single route is named `default` route.</span></span> <span data-ttu-id="2fa93-134">Większość aplikacji z kontrolerami i widokami używa szablonu trasy podobnego do `default` trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="2fa93-135">Interfejsy API REST powinny używać [routingu atrybutów](#ar).</span><span class="sxs-lookup"><span data-stu-id="2fa93-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="2fa93-136">Szablon trasy `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="2fa93-137">Dopasowuje ścieżkę URL, taką jak`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="2fa93-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="2fa93-138">Wyodrębnia wartości trasy `{ controller = Products, action = Details, id = 5 }` przez tokenizowanie ścieżki.</span><span class="sxs-lookup"><span data-stu-id="2fa93-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="2fa93-139">Wyodrębnienie wartości tras powoduje dopasowanie, jeśli aplikacja ma kontroler o nazwie `ProductsController` i `Details` akcję:</span><span class="sxs-lookup"><span data-stu-id="2fa93-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="2fa93-140">`/Products/Details/5`Model wiąże wartość, `id = 5` Aby ustawić parametr jako `id` `5` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="2fa93-141">Aby uzyskać więcej informacji, zobacz [powiązanie modelu](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="2fa93-142">`{controller=Home}`definiuje `Home` jako domyślny `controller` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="2fa93-143">`{action=Index}`definiuje `Index` jako domyślny `action` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="2fa93-144">`?`Znak w `{id?}` definiuje `id` jako opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="2fa93-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="2fa93-145">Domyślne i opcjonalne parametry trasy nie muszą być obecne w ścieżce URL dla dopasowania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="2fa93-146">Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="2fa93-147">Dopasowuje ścieżkę URL `/` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="2fa93-148">Tworzy wartości trasy `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="2fa93-149">Wartości dla `controller` i `action` używają wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="2fa93-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="2fa93-150">`id`nie produkuje wartości, ponieważ nie ma odpowiedniego segmentu w ścieżce adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="2fa93-151">`/`dopasowuje się tylko wtedy, gdy istnieje `HomeController` `Index` Akcja i:</span><span class="sxs-lookup"><span data-stu-id="2fa93-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="2fa93-152">Przy użyciu definicji kontrolera i szablonu trasy, `HomeController.Index` Akcja jest uruchamiana dla następujących ścieżek URL:</span><span class="sxs-lookup"><span data-stu-id="2fa93-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="2fa93-153">Ścieżka adresu URL `/` używa domyślnych `Home` kontrolerów i akcji szablonu trasy `Index` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="2fa93-154">Ścieżka adresu URL `/Home` używa domyślnej akcji szablonu trasy `Index` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="2fa93-155">Wygodna metoda <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> :</span><span class="sxs-lookup"><span data-stu-id="2fa93-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="2fa93-156">Zastępując</span><span class="sxs-lookup"><span data-stu-id="2fa93-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="2fa93-157">Routing jest konfigurowany przy użyciu programu <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> i <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="2fa93-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="2fa93-158">Aby użyć kontrolerów:</span><span class="sxs-lookup"><span data-stu-id="2fa93-158">To use controllers:</span></span>
>
> * <span data-ttu-id="2fa93-159">Wywołaj <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> wewnątrz `UseEndpoints` , aby zmapować kontrolery z [routingiem atrybutów](#ar) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="2fa93-160">Wywołaj <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> lub <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , aby zamapować zarówno kontrolery z [Konwencją routingu](#cr) , jak i kontrolery [przypisane do atrybutów](#ar) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="2fa93-161">Routing konwencjonalny</span><span class="sxs-lookup"><span data-stu-id="2fa93-161">Conventional routing</span></span>

<span data-ttu-id="2fa93-162">Routing konwencjonalny jest używany z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="2fa93-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="2fa93-163">`default`Trasa:</span><span class="sxs-lookup"><span data-stu-id="2fa93-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="2fa93-164">jest przykładem *konwencjonalnego routingu*.</span><span class="sxs-lookup"><span data-stu-id="2fa93-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="2fa93-165">Jest on nazywany *konwencjonalnym routingiem* , ponieważ ustanawia *Konwencję* dla ścieżek adresów URL:</span><span class="sxs-lookup"><span data-stu-id="2fa93-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="2fa93-166">Pierwszy segment ścieżki, `{controller=Home}` mapuje na nazwę kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="2fa93-167">Drugi segment, `{action=Index}` , mapuje na nazwę [akcji](#action) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="2fa93-168">Trzeci segment `{id?}` jest używany jako opcjonalny `id` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="2fa93-169">`?`W programie w programie `{id?}` jest to opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="2fa93-170">`id`służy do mapowania na jednostkę modelu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="2fa93-171">Przy użyciu tej `default` trasy ścieżka URL:</span><span class="sxs-lookup"><span data-stu-id="2fa93-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="2fa93-172">`/Products/List`mapuje do `ProductsController.List` akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="2fa93-173">`/Blog/Article/17`mapowanie na `BlogController.Article` model i zwykle wiąże się z `id` parametrem 17.</span><span class="sxs-lookup"><span data-stu-id="2fa93-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="2fa93-174">To mapowanie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-174">This mapping:</span></span>

* <span data-ttu-id="2fa93-175">Jest oparty **tylko**na nazwach kontrolera i [akcji](#action) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="2fa93-176">Nie jest oparty na przestrzeniach nazw, lokalizacjach plików źródłowych ani parametrach metod.</span><span class="sxs-lookup"><span data-stu-id="2fa93-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="2fa93-177">Użycie konwencjonalnego routingu z domyślną trasą umożliwia tworzenie aplikacji bez konieczności korzystania z nowego wzorca adresu URL dla każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="2fa93-178">W przypadku aplikacji z akcjami w stylu [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) mających spójność dla adresów URL między kontrolerami:</span><span class="sxs-lookup"><span data-stu-id="2fa93-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="2fa93-179">Upraszcza kod.</span><span class="sxs-lookup"><span data-stu-id="2fa93-179">Helps simplify the code.</span></span>
* <span data-ttu-id="2fa93-180">Sprawia, że interfejs użytkownika jest bardziej przewidywalny.</span><span class="sxs-lookup"><span data-stu-id="2fa93-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="2fa93-181">`id`W powyższym kodzie jest zdefiniowany jako opcjonalny przez szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="2fa93-182">Akcje można wykonać bez opcjonalnego identyfikatora podanego w ramach adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="2fa93-183">Ogólnie mówiąc, gdy `id` pominięto w adresie URL:</span><span class="sxs-lookup"><span data-stu-id="2fa93-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="2fa93-184">`id`jest ustawiony na `0` przez powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="2fa93-185">Nie znaleziono jednostki w dopasowaniu do bazy danych `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="2fa93-186">[Routing atrybutu](#ar) zawiera szczegółowy formant, który umożliwia określanie identyfikatora wymaganego dla niektórych akcji, a nie dla innych.</span><span class="sxs-lookup"><span data-stu-id="2fa93-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="2fa93-187">Zgodnie z Konwencją, dokumentacja zawiera opcjonalne parametry, takie jak, `id` gdy prawdopodobnie pojawi się w prawidłowym użyciu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="2fa93-188">Większość aplikacji powinna wybrać podstawowy i opisowy schemat routingu, aby adresy URL były czytelne i zrozumiałe.</span><span class="sxs-lookup"><span data-stu-id="2fa93-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="2fa93-189">Domyślna trasa konwencjonalna `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="2fa93-190">Obsługuje podstawowy i opisowy schemat routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="2fa93-191">Jest użytecznym punktem wyjścia dla aplikacji opartych na interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2fa93-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="2fa93-192">Jest jedynym szablonem trasy wymaganym przez wiele aplikacji interfejsu użytkownika sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2fa93-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="2fa93-193">W przypadku większych aplikacji interfejsu użytkownika sieci Web inna trasa korzysta z [obszarów](#areas) , jeśli są one często używane.</span><span class="sxs-lookup"><span data-stu-id="2fa93-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="2fa93-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>i <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="2fa93-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="2fa93-195">Automatycznie Przypisz wartość **zamówienia** do punktów końcowych na podstawie kolejności, w której są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="2fa93-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="2fa93-196">Routing punktów końcowych w ASP.NET Core 3,0 i nowszych:</span><span class="sxs-lookup"><span data-stu-id="2fa93-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="2fa93-197">Nie ma koncepcji tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="2fa93-198">Nie oferuje gwarancji porządkowania dla wykonywania rozszerzalności, wszystkie punkty końcowe są przetwarzane jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="2fa93-199">Włącz [Rejestrowanie](xref:fundamentals/logging/index) , aby zobaczyć, jak wbudowane implementacje routingu, takie jak <xref:Microsoft.AspNetCore.Routing.Route> , dopasowują żądania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="2fa93-200">[Routing atrybutów](#ar) został wyjaśniony w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="2fa93-201">Wiele konwencjonalnych tras</span><span class="sxs-lookup"><span data-stu-id="2fa93-201">Multiple conventional routes</span></span>

<span data-ttu-id="2fa93-202">Wewnątrz można dodać wiele [konwencjonalnych tras](#cr) `UseEndpoints` , dodając więcej wywołań do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> i <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="2fa93-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="2fa93-203">Dzięki temu można definiować wiele Konwencji lub dodać do nich trasy konwencjonalne, które są przeznaczone dla konkretnej [akcji](#action), na przykład:</span><span class="sxs-lookup"><span data-stu-id="2fa93-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="2fa93-204">`blog`Trasa w powyższym kodzie jest **dedykowaną konwencjonalną trasą**.</span><span class="sxs-lookup"><span data-stu-id="2fa93-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="2fa93-205">Jest on nazywany dedykowaną trasą konwencjonalną, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="2fa93-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="2fa93-206">Używa ona [konwencjonalnego routingu](#cr).</span><span class="sxs-lookup"><span data-stu-id="2fa93-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="2fa93-207">Jest on przeznaczony dla konkretnej [akcji](#action).</span><span class="sxs-lookup"><span data-stu-id="2fa93-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="2fa93-208">Ponieważ `controller` i `action` nie pojawiają się w szablonie trasy `"blog/{*article}"` jako parametry:</span><span class="sxs-lookup"><span data-stu-id="2fa93-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="2fa93-209">Mogą mieć tylko wartości domyślne `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="2fa93-210">Ta trasa zawsze jest mapowana na akcję `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="2fa93-211">`/Blog`, `/Blog/Article` i `/Blog/{any-string}` są jedynymi ŚCIEŻKAmi URL, które pasują do trasy blogu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="2fa93-212">Powyższy przykład:</span><span class="sxs-lookup"><span data-stu-id="2fa93-212">The preceding example:</span></span>

* <span data-ttu-id="2fa93-213">`blog`trasa ma wyższy priorytet dla dopasowania niż trasa, `default` ponieważ jest dodawana jako pierwsza.</span><span class="sxs-lookup"><span data-stu-id="2fa93-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="2fa93-214">Jest i przykładem routingu stylów [informacji](https://developer.mozilla.org/docs/Glossary/Slug) o sobie, w którym typowym jest nazwa artykułu w ramach adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="2fa93-215">W ASP.NET Core 3,0 i nowszych routingu nie są:</span><span class="sxs-lookup"><span data-stu-id="2fa93-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="2fa93-216">Zdefiniuj koncepcję o nazwie *trasa*.</span><span class="sxs-lookup"><span data-stu-id="2fa93-216">Define a concept called a *route*.</span></span> <span data-ttu-id="2fa93-217">`UseRouting`dodaje dopasowanie trasy do potoku programu pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="2fa93-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="2fa93-218">`UseRouting`Oprogramowanie pośredniczące sprawdza zestaw punktów końcowych zdefiniowanych w aplikacji i wybiera najlepsze dopasowanie punktu końcowego na podstawie żądania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="2fa93-219">Podaj gwarancje dotyczące kolejności wykonywania rozszerzalności, takich jak <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> lub <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="2fa93-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="2fa93-220">Zobacz [Routing](xref:fundamentals/routing) dla materiałów referencyjnych na trasie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="2fa93-221">Tradycyjna kolejność routingu</span><span class="sxs-lookup"><span data-stu-id="2fa93-221">Conventional routing order</span></span>

<span data-ttu-id="2fa93-222">Routing konwencjonalny pasuje do kombinacji akcji i kontrolera, które są zdefiniowane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="2fa93-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="2fa93-223">Jest to przeznaczone do uproszczenia sytuacji, w których trasy konwencjonalne nakładają się na siebie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="2fa93-224">Dodanie tras przy użyciu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> i <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatycznie przypisanie wartości zamówienia do punktów końcowych w oparciu o kolejność, w której są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="2fa93-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="2fa93-225">Dopasowania z trasy, która pojawiła się wcześniej, mają wyższy priorytet.</span><span class="sxs-lookup"><span data-stu-id="2fa93-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="2fa93-226">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2fa93-227">Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczone wcześniej, ponieważ są bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="2fa93-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="2fa93-228">[Dedykowane konwencjonalne trasy](#dcr) ze wszystkimi parametrami tras, takich jak, `{*article}` mogą spowodować zbyt [zachłanne](xref:fundamentals/routing#greedy)trasy, co oznacza, że pasuje do adresów URL, które mają być dopasowane przez inne trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="2fa93-229">Umieść trasy zachłanne w dalszej części tabeli tras, aby zapobiec dopasowania zachłanne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="2fa93-230">Rozwiązywanie niejednoznacznych akcji</span><span class="sxs-lookup"><span data-stu-id="2fa93-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="2fa93-231">Gdy dwa punkty końcowe pasują do routingu, routing musi wykonać jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="2fa93-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="2fa93-232">Wybierz najlepszego kandydata.</span><span class="sxs-lookup"><span data-stu-id="2fa93-232">Choose the best candidate.</span></span>
* <span data-ttu-id="2fa93-233">Zgłoś wyjątek.</span><span class="sxs-lookup"><span data-stu-id="2fa93-233">Throw an exception.</span></span>

<span data-ttu-id="2fa93-234">Przykład:</span><span class="sxs-lookup"><span data-stu-id="2fa93-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="2fa93-235">Poprzedni kontroler definiuje dwie akcje, które są zgodne:</span><span class="sxs-lookup"><span data-stu-id="2fa93-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="2fa93-236">Ścieżka adresu URL`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="2fa93-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="2fa93-237">Kierowanie danych `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="2fa93-238">Jest to typowy wzorzec dla kontrolerów MVC:</span><span class="sxs-lookup"><span data-stu-id="2fa93-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="2fa93-239">`Edit(int)`Wyświetla formularz, w którym można edytować produkt.</span><span class="sxs-lookup"><span data-stu-id="2fa93-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="2fa93-240">`Edit(int, Product)`przetwarza opublikowany formularz.</span><span class="sxs-lookup"><span data-stu-id="2fa93-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="2fa93-241">Aby rozwiązać poprawność trasy:</span><span class="sxs-lookup"><span data-stu-id="2fa93-241">To resolve the correct route:</span></span>

* <span data-ttu-id="2fa93-242">`Edit(int, Product)`jest wybierany, gdy żądanie jest HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="2fa93-243">`Edit(int)`jest wybierany, gdy [czasownik http](#verb) jest inny.</span><span class="sxs-lookup"><span data-stu-id="2fa93-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="2fa93-244">`Edit(int)`jest zazwyczaj wywoływany za pośrednictwem `GET` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="2fa93-245"><xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]` ,, Jest dostarczany do routingu, aby można było wybrać oparty na metodzie HTTP żądania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="2fa93-246">`HttpPostAttribute`Zapewnia `Edit(int, Product)` lepszy odpowiednik niż `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="2fa93-247">Ważne jest, aby zrozumieć rolę atrybutów, takich jak `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="2fa93-248">Podobne atrybuty są zdefiniowane dla innych [czasowników HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="2fa93-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="2fa93-249">W przypadku [routingu konwencjonalnego](#cr)typowe dla akcji używanie tej samej nazwy akcji, gdy są one częścią formularza Pokaż, przesyłania formularza.</span><span class="sxs-lookup"><span data-stu-id="2fa93-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="2fa93-250">Na przykład zapoznaj [się z tematem badanie dwóch metod akcji edycji](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="2fa93-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="2fa93-251">Jeśli routingu nie można wybrać najlepszego kandydata, <xref:System.Reflection.AmbiguousMatchException> zostanie zgłoszony, zawierający wiele pasujących punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="2fa93-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="2fa93-252">Nazwy tras konwencjonalnych</span><span class="sxs-lookup"><span data-stu-id="2fa93-252">Conventional route names</span></span>

<span data-ttu-id="2fa93-253">Ciągi `"blog"` i `"default"` w poniższych przykładach są nazwami konwencjonalnych tras:</span><span class="sxs-lookup"><span data-stu-id="2fa93-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="2fa93-254">Nazwy tras nadają trasie nazwę logiczną.</span><span class="sxs-lookup"><span data-stu-id="2fa93-254">The route names give the route a logical name.</span></span> <span data-ttu-id="2fa93-255">Nazwana trasa może służyć do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="2fa93-256">Użycie nazwanej trasy upraszcza tworzenie adresów URL, gdy porządkowanie tras może spowodować skomplikowane generowanie adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="2fa93-257">Nazwy tras muszą być unikatowe w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="2fa93-258">Nazwy tras:</span><span class="sxs-lookup"><span data-stu-id="2fa93-258">Route names:</span></span>

* <span data-ttu-id="2fa93-259">Nie mają wpływu na pasujące adresy URL ani obsługę żądań.</span><span class="sxs-lookup"><span data-stu-id="2fa93-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="2fa93-260">Są używane tylko do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-260">Are used only for URL generation.</span></span>

<span data-ttu-id="2fa93-261">Koncepcja nazwy trasy jest reprezentowana w obszarze Routing jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="2fa93-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="2fa93-262">**Nazwa trasy** i nazwa **punktu końcowego**:</span><span class="sxs-lookup"><span data-stu-id="2fa93-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="2fa93-263">Są zamienne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-263">Are interchangeable.</span></span>
* <span data-ttu-id="2fa93-264">Który jest używany w dokumentacji i kodu zależy od opisanego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="2fa93-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="2fa93-265">Routing atrybutów dla interfejsów API REST</span><span class="sxs-lookup"><span data-stu-id="2fa93-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="2fa93-266">Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcjonalności aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez [zlecenia http](#verb).</span><span class="sxs-lookup"><span data-stu-id="2fa93-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="2fa93-267">Funkcja routingu atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="2fa93-268">Poniższy `StartUp.Configure` kod jest typowy dla interfejsu API REST i jest używany w następnym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="2fa93-269">W powyższym kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> jest wywoływana w `UseEndpoints` celu mapowania kontrolerów z routingiem atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="2fa93-270">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-270">In the following example:</span></span>

* <span data-ttu-id="2fa93-271">Poprzednia `Configure` Metoda jest używana.</span><span class="sxs-lookup"><span data-stu-id="2fa93-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="2fa93-272">`HomeController`dopasowuje zestaw adresów URL podobny do tego, co jest zgodne z domyślną trasą konwencjonalny `{controller=Home}/{action=Index}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="2fa93-273">`HomeController.Index`Akcja jest uruchamiana dla dowolnej ścieżki URL,, `/` `/Home` `/Home/Index` , lub `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="2fa93-274">W tym przykładzie przedstawiono najważniejsze różnice programistyczne między routingiem atrybutów i [routingiem konwencjonalnym](#cr).</span><span class="sxs-lookup"><span data-stu-id="2fa93-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="2fa93-275">Routing atrybutów wymaga więcej danych wejściowych w celu określenia trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="2fa93-276">Konwencjonalne trasy domyślne obsługuje trasy bardziej zwięzłie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="2fa93-277">Jednak Routing atrybutu zezwala na ścisłą kontrolę nad tym, które szablony tras mają zastosowanie do poszczególnych [akcji](#action).</span><span class="sxs-lookup"><span data-stu-id="2fa93-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="2fa93-278">W przypadku routingu atrybutów nazwy kontrolerów i akcji nie odgrywają części, w której akcja jest dopasowywana, chyba że zostanie użyta [Zastępowanie tokenu](#routing-token-replacement-templates-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-278">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="2fa93-279">Poniższy przykład dopasowuje te same adresy URL, co w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-279">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="2fa93-280">Następujący kod używa zastąpienia tokenu dla `action` i `controller` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-280">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="2fa93-281">Następujący kod ma zastosowanie `[Route("[controller]/[action]")]` do kontrolera:</span><span class="sxs-lookup"><span data-stu-id="2fa93-281">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="2fa93-282">W poprzednim kodzie, `Index` Szablony metod muszą być poprzedzone `/` lub `~/` do szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-282">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="2fa93-283">Szablony tras zastosowane do akcji rozpoczynającej się od `/` lub `~/` nie są połączone z szablonami tras zastosowanymi do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-283">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="2fa93-284">Zobacz [pierwszeństwo szablonu trasy](xref:fundamentals/routing#rtp) , aby uzyskać informacje o wyborze szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-284">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="2fa93-285">Zastrzeżone nazwy routingu</span><span class="sxs-lookup"><span data-stu-id="2fa93-285">Reserved routing names</span></span>

<span data-ttu-id="2fa93-286">Następujące słowa kluczowe są zastrzeżonymi nazwami parametrów trasy podczas korzystania z kontrolerów lub Razor stron:</span><span class="sxs-lookup"><span data-stu-id="2fa93-286">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="2fa93-287">Użycie `page` jako parametru trasy z routingiem atrybutu jest typowym błędem.</span><span class="sxs-lookup"><span data-stu-id="2fa93-287">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="2fa93-288">Powoduje to niespójne i mylące zachowanie przy generowaniu adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-288">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="2fa93-289">Specjalne nazwy parametrów są używane przez generowanie adresów URL w celu ustalenia, czy operacja generowania adresu URL odwołuje się do Razor strony lub do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-289">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="2fa93-290">Szablony czasowników HTTP</span><span class="sxs-lookup"><span data-stu-id="2fa93-290">HTTP verb templates</span></span>

<span data-ttu-id="2fa93-291">ASP.NET Core ma następujące szablony czasowników HTTP:</span><span class="sxs-lookup"><span data-stu-id="2fa93-291">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="2fa93-292">[Narzędzia HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="2fa93-292">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="2fa93-293">[HTTPPOST](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="2fa93-293">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="2fa93-294">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="2fa93-294">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="2fa93-295">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="2fa93-295">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="2fa93-296">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="2fa93-296">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="2fa93-297">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="2fa93-297">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="2fa93-298">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="2fa93-298">Route templates</span></span>

<span data-ttu-id="2fa93-299">ASP.NET Core ma następujące szablony tras:</span><span class="sxs-lookup"><span data-stu-id="2fa93-299">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="2fa93-300">Wszystkie [Szablony czasowników HTTP](#verb) są szablonami tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-300">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="2fa93-301">[Szlak](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="2fa93-301">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="2fa93-302">Routing atrybutów z atrybutami czasownik http</span><span class="sxs-lookup"><span data-stu-id="2fa93-302">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="2fa93-303">Rozważmy następujący kontroler:</span><span class="sxs-lookup"><span data-stu-id="2fa93-303">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="2fa93-304">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-304">In the preceding code:</span></span>

* <span data-ttu-id="2fa93-305">Każda akcja zawiera `[HttpGet]` atrybut, który ogranicza dopasowywanie do żądań HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="2fa93-305">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="2fa93-306">`GetProduct`Akcja obejmuje `"{id}"` szablon, dlatego `id` jest dołączany do `"api/[controller]"` szablonu na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="2fa93-306">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="2fa93-307">Szablon metod to `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-307">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="2fa93-308">W związku z tym ta akcja dopasowuje tylko żądania GET dla formularza `/api/test2/xyz` ,, `/api/test2/123` `/api/test2/{any string}` itp.</span><span class="sxs-lookup"><span data-stu-id="2fa93-308">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="2fa93-309">`GetIntProduct`Akcja zawiera `"int/{id:int}")` szablon.</span><span class="sxs-lookup"><span data-stu-id="2fa93-309">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="2fa93-310">`:int`Część szablonu ogranicza `id` wartości trasy do ciągów, które mogą być konwertowane na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="2fa93-310">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="2fa93-311">Żądanie GET `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-311">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="2fa93-312">Nie jest zgodna z tą akcją.</span><span class="sxs-lookup"><span data-stu-id="2fa93-312">Doesn't match this action.</span></span>
  * <span data-ttu-id="2fa93-313">Zwraca błąd [nie znaleziono 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-313">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="2fa93-314">`GetInt2Product`Akcja zawiera `{id}` w szablonie, ale nie ogranicza `id` wartości, które można przekonwertować na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="2fa93-314">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="2fa93-315">Żądanie GET `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-315">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="2fa93-316">Pasuje do tej trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-316">Matches this route.</span></span>
  * <span data-ttu-id="2fa93-317">Nie można przekonwertować powiązania modelu `abc` na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="2fa93-317">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="2fa93-318">`id`Parametr metody jest liczbą całkowitą.</span><span class="sxs-lookup"><span data-stu-id="2fa93-318">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="2fa93-319">Zwraca [400 Nieprawidłowe żądanie](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , ponieważ nie można skonwertować powiązania modelu `abc` na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="2fa93-319">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="2fa93-320">Funkcja routingu atrybutów może używać <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> atrybutów takich jak <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> , i <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="2fa93-320">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="2fa93-321">Wszystkie atrybuty [czasowników HTTP](#verb) akceptują szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-321">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="2fa93-322">Poniższy przykład przedstawia dwie akcje, które pasują do tego samego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="2fa93-322">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="2fa93-323">Przy użyciu ścieżki URL `/products3` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-323">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="2fa93-324">`MyProductsController.ListProducts`Akcja jest uruchamiana, gdy [czasownik http](#verb) to `GET` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-324">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="2fa93-325">`MyProductsController.CreateProduct`Akcja jest uruchamiana, gdy [czasownik http](#verb) to `POST` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-325">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="2fa93-326">Podczas kompilowania interfejsu API REST jest to rzadko konieczne, aby użyć `[Route(...)]` metody akcji, ponieważ akcja akceptuje wszystkie metody http.</span><span class="sxs-lookup"><span data-stu-id="2fa93-326">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="2fa93-327">Lepiej jest używać bardziej szczegółowego [atrybutu zlecenia http](#verb) , aby precyzyjnie dowiedzieć się, co obsługuje interfejs API.</span><span class="sxs-lookup"><span data-stu-id="2fa93-327">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="2fa93-328">Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i czasowniki HTTP mapują na określone operacje logiczne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-328">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="2fa93-329">Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcjonalności aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez zlecenia HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fa93-329">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="2fa93-330">Oznacza to, że wiele operacji, na przykład Pobierz i Opublikuj dla tego samego zasobu logicznego, użyj tego samego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-330">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="2fa93-331">Routing atrybutu zapewnia poziom kontroli, który jest wymagany do dokładnego projektowania układu publicznego punktu końcowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="2fa93-331">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="2fa93-332">Ponieważ atrybut Route ma zastosowanie do określonej akcji, można łatwo wprowadzić parametry wymagane jako część definicji szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-332">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="2fa93-333">W poniższym przykładzie `id` jest wymagane jako część ścieżki URL:</span><span class="sxs-lookup"><span data-stu-id="2fa93-333">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="2fa93-334">`Products2ApiController.GetProduct(int)`Akcja:</span><span class="sxs-lookup"><span data-stu-id="2fa93-334">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="2fa93-335">Jest uruchamiany z ścieżką URL podobną do`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="2fa93-335">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="2fa93-336">Nie jest uruchamiany z ścieżką URL `/products2` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-336">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="2fa93-337">Atrybut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) Requests] umożliwia akcja ograniczenia obsługiwanych typów zawartości żądania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-337">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="2fa93-338">Aby uzyskać więcej informacji, zobacz [Definiowanie obsługiwanych typów zawartości żądania przy użyciu atrybutu użycia](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="2fa93-338">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="2fa93-339">Aby uzyskać pełny opis szablonów tras i powiązanych opcji, zobacz [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-339">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="2fa93-340">Aby uzyskać więcej informacji na temat `[ApiController]` , zobacz [ApiController Attribute](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="2fa93-340">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="2fa93-341">Nazwa trasy</span><span class="sxs-lookup"><span data-stu-id="2fa93-341">Route name</span></span>

<span data-ttu-id="2fa93-342">Następujący kod definiuje nazwę trasy `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-342">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="2fa93-343">Nazwy tras mogą służyć do generowania adresów URL na podstawie określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-343">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="2fa93-344">Nazwy tras:</span><span class="sxs-lookup"><span data-stu-id="2fa93-344">Route names:</span></span>

* <span data-ttu-id="2fa93-345">Nie ma wpływu na zachowanie w zakresie routingu zgodnego z adresem URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-345">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="2fa93-346">Są używane tylko na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-346">Are only used for URL generation.</span></span>

<span data-ttu-id="2fa93-347">Nazwy tras muszą być unikatowe w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-347">Route names must be unique application-wide.</span></span>

<span data-ttu-id="2fa93-348">Powyższy kod przy użyciu konwencjonalnej trasy domyślnej, która definiuje `id` parametr jako opcjonalny ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="2fa93-348">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="2fa93-349">Możliwość precyzyjnego określania interfejsów API ma zalety, takie jak umożliwienie `/products` i `/products/5` wysyłanie ich do różnych akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-349">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="2fa93-350">Łączenie tras atrybutów</span><span class="sxs-lookup"><span data-stu-id="2fa93-350">Combining attribute routes</span></span>

<span data-ttu-id="2fa93-351">Aby mniej powtarzać Routing atrybutów, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-351">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="2fa93-352">Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzone w celu rozesłania szablonów w akcjach.</span><span class="sxs-lookup"><span data-stu-id="2fa93-352">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="2fa93-353">Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w kontrolerze używają routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-353">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="2fa93-354">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-354">In the preceding example:</span></span>

* <span data-ttu-id="2fa93-355">Ścieżka adresu URL `/products` może być taka sama`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="2fa93-355">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="2fa93-356">Ścieżka adresu URL `/products/5` może być taka sama `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-356">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="2fa93-357">Obie te akcje pasują tylko do protokołu HTTP, `GET` ponieważ są oznaczone `[HttpGet]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="2fa93-357">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="2fa93-358">Szablony tras zastosowane do akcji rozpoczynającej się od `/` lub `~/` nie są połączone z szablonami tras zastosowanymi do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-358">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="2fa93-359">Poniższy przykład dopasowuje zestaw ścieżek adresów URL podobny do trasy domyślnej.</span><span class="sxs-lookup"><span data-stu-id="2fa93-359">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="2fa93-360">W poniższej tabeli opisano `[Route]` atrybuty w poprzednim kodzie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-360">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="2fa93-361">Atrybut</span><span class="sxs-lookup"><span data-stu-id="2fa93-361">Attribute</span></span>               | <span data-ttu-id="2fa93-362">Łączy z`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="2fa93-362">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="2fa93-363">Definiuje szablon trasy</span><span class="sxs-lookup"><span data-stu-id="2fa93-363">Defines route template</span></span> |
| ---
<span data-ttu-id="2fa93-364">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-364">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-365">'Blazor'</span></span>
- <span data-ttu-id="2fa93-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-366">'Identity'</span></span>
- <span data-ttu-id="2fa93-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-368">'Razor'</span></span>
- <span data-ttu-id="2fa93-369">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fa93-370">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-370">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-371">'Blazor'</span></span>
- <span data-ttu-id="2fa93-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-372">'Identity'</span></span>
- <span data-ttu-id="2fa93-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-374">'Razor'</span></span>
- <span data-ttu-id="2fa93-375">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-375">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fa93-376">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-376">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-377">'Blazor'</span></span>
- <span data-ttu-id="2fa93-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-378">'Identity'</span></span>
- <span data-ttu-id="2fa93-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-380">'Razor'</span></span>
- <span data-ttu-id="2fa93-381">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fa93-382">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-382">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-383">'Blazor'</span></span>
- <span data-ttu-id="2fa93-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-384">'Identity'</span></span>
- <span data-ttu-id="2fa93-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-386">'Razor'</span></span>
- <span data-ttu-id="2fa93-387">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fa93-388">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-388">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-389">'Blazor'</span></span>
- <span data-ttu-id="2fa93-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-390">'Identity'</span></span>
- <span data-ttu-id="2fa93-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-392">'Razor'</span></span>
- <span data-ttu-id="2fa93-393">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fa93-394">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-394">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-395">'Blazor'</span></span>
- <span data-ttu-id="2fa93-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-396">'Identity'</span></span>
- <span data-ttu-id="2fa93-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-398">'Razor'</span></span>
- <span data-ttu-id="2fa93-399">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-399">'SignalR' uid:</span></span> 

<span data-ttu-id="2fa93-400">--------- | ---title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-400">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-401">'Blazor'</span></span>
- <span data-ttu-id="2fa93-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-402">'Identity'</span></span>
- <span data-ttu-id="2fa93-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-404">'Razor'</span></span>
- <span data-ttu-id="2fa93-405">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fa93-406">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-406">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-407">'Blazor'</span></span>
- <span data-ttu-id="2fa93-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-408">'Identity'</span></span>
- <span data-ttu-id="2fa93-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-410">'Razor'</span></span>
- <span data-ttu-id="2fa93-411">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fa93-412">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-412">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-413">'Blazor'</span></span>
- <span data-ttu-id="2fa93-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-414">'Identity'</span></span>
- <span data-ttu-id="2fa93-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-416">'Razor'</span></span>
- <span data-ttu-id="2fa93-417">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fa93-418">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-418">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-419">'Blazor'</span></span>
- <span data-ttu-id="2fa93-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-420">'Identity'</span></span>
- <span data-ttu-id="2fa93-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-422">'Razor'</span></span>
- <span data-ttu-id="2fa93-423">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-423">'SignalR' uid:</span></span> 

<span data-ttu-id="2fa93-424">------ | ---title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-424">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-425">'Blazor'</span></span>
- <span data-ttu-id="2fa93-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-426">'Identity'</span></span>
- <span data-ttu-id="2fa93-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-428">'Razor'</span></span>
- <span data-ttu-id="2fa93-429">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fa93-430">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fa93-430">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fa93-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-431">'Blazor'</span></span>
- <span data-ttu-id="2fa93-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fa93-432">'Identity'</span></span>
- <span data-ttu-id="2fa93-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fa93-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fa93-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fa93-434">'Razor'</span></span>
- <span data-ttu-id="2fa93-435">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2fa93-435">'SignalR' uid:</span></span> 

<span data-ttu-id="2fa93-436">----- | | `[Route("")]` | Tak | `"Home"` |
| `[Route("Index")]` | Tak | `"Home/Index"` |
| `[Route("/")]` | **Nie** | `""` |
 | `[Route("About")]` | Tak | `"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="2fa93-436">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="2fa93-437">Porządek trasy dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="2fa93-437">Attribute route order</span></span>

<span data-ttu-id="2fa93-438">Routing kompiluje drzewo i dopasowuje wszystkie punkty końcowe jednocześnie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-438">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="2fa93-439">Wpisy trasy zachowują się tak, jakby zostały umieszczone w idealnej kolejności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-439">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="2fa93-440">Najbardziej konkretne trasy mają możliwość wykonania przed bardziej ogólnymi trasami.</span><span class="sxs-lookup"><span data-stu-id="2fa93-440">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="2fa93-441">Na przykład, podobnie jak w przypadku `blog/search/{topic}` bardziej szczegółowych informacji niż trasie atrybutu `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-441">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="2fa93-442">`blog/search/{topic}`Trasa ma wyższy priorytet, ponieważ jest domyślnie bardziej specyficzna.</span><span class="sxs-lookup"><span data-stu-id="2fa93-442">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="2fa93-443">Przy użyciu [konwencjonalnego routingu](#cr)deweloper jest odpowiedzialny za umieszczanie tras w odpowiedniej kolejności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-443">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="2fa93-444">Trasy atrybutów mogą konfigurować zamówienie przy użyciu <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> właściwości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-444">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="2fa93-445">Wszystkie [atrybuty trasy](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) dostarczonej przez platformę obejmują `Order` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-445">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="2fa93-446">Trasy są przetwarzane zgodnie z rosnącą sortowaniem `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-446">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="2fa93-447">Kolejność domyślna to `0` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-447">The default order is `0`.</span></span> <span data-ttu-id="2fa93-448">Ustawianie trasy przy użyciu `Order = -1` przebiegów przed trasami, które nie ustawiają kolejności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-448">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="2fa93-449">Ustawianie trasy przy użyciu `Order = 1` przebiegu po domyślnej kolejności tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-449">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="2fa93-450">**Należy unikać** w zależności od `Order` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-450">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="2fa93-451">Jeśli przestrzeń adresów URL aplikacji wymaga jawnych wartości kolejności, aby można było prawidłowo kierować trasy, prawdopodobnie również jest myląca dla klientów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-451">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="2fa93-452">Ogólnie rzecz biorąc, routing atrybutów wybiera prawidłową trasę z dopasowywaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-452">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="2fa93-453">Jeśli domyślna kolejność generowania adresów URL nie działa, użycie nazwy trasy jako przesłonięcia jest zwykle prostsze niż stosowanie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-453">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="2fa93-454">Należy wziąć pod uwagę następujące dwa kontrolery, które definiują trasę zgodną z `/home` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-454">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="2fa93-455">Żądanie `/home` z powyższym kodem zgłasza wyjątek podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="2fa93-455">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="2fa93-456">Dodanie `Order` do jednego z atrybutów trasy rozwiązuje niejednoznaczność:</span><span class="sxs-lookup"><span data-stu-id="2fa93-456">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="2fa93-457">Za pomocą powyższego kodu program `/home` uruchamia `HomeController.Index` punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-457">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="2fa93-458">, Aby przejść do `MyDemoController.MyIndex` żądania `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-458">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="2fa93-459">**Uwaga**:</span><span class="sxs-lookup"><span data-stu-id="2fa93-459">**Note**:</span></span>

* <span data-ttu-id="2fa93-460">Poprzedni kod jest przykładem lub słabym projektem routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-460">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="2fa93-461">Została użyta do zilustrowania `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-461">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="2fa93-462">`Order`Właściwość rozwiązuje tylko niejednoznaczność, ale nie można dopasować tego szablonu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-462">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="2fa93-463">Lepszym rozwiązaniem jest usunięcie `[Route("Home")]` szablonu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-463">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="2fa93-464">Zobacz [ Razor strony trasy i konwencje aplikacji: kolejność tras](xref:razor-pages/razor-pages-conventions#route-order) na potrzeby informacji o kolejności tras na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="2fa93-464">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="2fa93-465">W niektórych przypadkach błąd HTTP 500 jest zwracany z niejednoznacznych tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-465">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="2fa93-466">Użyj [rejestrowania](xref:fundamentals/logging/index) , aby zobaczyć, które punkty końcowe spowodowały `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-466">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="2fa93-467">Zastępowanie tokenu w szablonach tras [Controller], [Action], [obszar]</span><span class="sxs-lookup"><span data-stu-id="2fa93-467">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="2fa93-468">Dla wygody atrybut trasy obsługują zastępowanie tokenów dla zarezerwowanych parametrów trasy przez załączanie tokenu w jednym z następujących:</span><span class="sxs-lookup"><span data-stu-id="2fa93-468">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="2fa93-469">Nawiasy kwadratowe:`[]`</span><span class="sxs-lookup"><span data-stu-id="2fa93-469">Square brackets: `[]`</span></span>
* <span data-ttu-id="2fa93-470">Nawiasy klamrowe:`{}`</span><span class="sxs-lookup"><span data-stu-id="2fa93-470">Curly braces: `{}`</span></span>

<span data-ttu-id="2fa93-471">Tokeny `[action]` , `[area]` i `[controller]` są zastępowane wartościami nazwy akcji, obszaru i nazwy kontrolera z akcji, w której zdefiniowano trasę:</span><span class="sxs-lookup"><span data-stu-id="2fa93-471">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="2fa93-472">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-472">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="2fa93-473">Prawdopodobny`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="2fa93-473">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="2fa93-474">Prawdopodobny`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="2fa93-474">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="2fa93-475">Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-475">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="2fa93-476">Poprzedni przykład zachowuje się tak samo jak w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-476">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="2fa93-477">Trasy atrybutu można także łączyć z dziedziczeniem.</span><span class="sxs-lookup"><span data-stu-id="2fa93-477">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="2fa93-478">Jest to zaawansowane połączenie z zastępowaniem tokenu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-478">This is powerful combined with token replacement.</span></span> <span data-ttu-id="2fa93-479">Zastępowanie tokenu dotyczy również nazw tras zdefiniowanych przez trasy atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-479">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="2fa93-480">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji:</span><span class="sxs-lookup"><span data-stu-id="2fa93-480">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="2fa93-481">Zastępowanie tokenu dotyczy również nazw tras zdefiniowanych przez trasy atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-481">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="2fa93-482">generuje unikatową nazwę trasy dla każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-482">generates a unique route name for each action.</span></span>

<span data-ttu-id="2fa93-483">Aby dopasować ogranicznik zamiany tokenu literału `[` lub `]` , należy to zrobić, powtarzając znak ( `[[` lub `]]` ).</span><span class="sxs-lookup"><span data-stu-id="2fa93-483">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="2fa93-484">Używanie transformatora parametrów do dostosowywania zastępowania tokenu</span><span class="sxs-lookup"><span data-stu-id="2fa93-484">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="2fa93-485">Zastępowanie tokenu można dostosować za pomocą transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-485">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="2fa93-486">Transformator parametrów implementuje <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-486">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="2fa93-487">Na przykład niestandardowy `SlugifyParameterTransformer` transformator parametrów zmienia `SubscriptionManagement` wartość trasy na `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-487">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="2fa93-488"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention>Jest konwencją modelu aplikacji, która:</span><span class="sxs-lookup"><span data-stu-id="2fa93-488">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="2fa93-489">Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-489">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="2fa93-490">Dostosowuje wartości tokenów tras w miarę ich wymiany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-490">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="2fa93-491">Poprzednia `ListAll` Metoda pasuje `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-491">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="2fa93-492">`RouteTokenTransformerConvention`Jest zarejestrowany jako opcja w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-492">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="2fa93-493">Zobacz [powiadomienia MDN Web docs](https://developer.mozilla.org/docs/Glossary/Slug) , aby zapoznać się z definicją informacji o sobie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-493">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="2fa93-494">Wiele tras atrybutów</span><span class="sxs-lookup"><span data-stu-id="2fa93-494">Multiple attribute routes</span></span>

<span data-ttu-id="2fa93-495">Routing atrybutów obsługuje definiowanie wielu tras, które docierają do tej samej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-495">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="2fa93-496">Najczęstszym sposobem użycia tej metody jest naśladowanie zachowania domyślnej trasy konwencjonalnej, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-496">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="2fa93-497">Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każda z nich łączy się z każdym z atrybutów tras w metodach akcji:</span><span class="sxs-lookup"><span data-stu-id="2fa93-497">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="2fa93-498">Wszystkie ograniczenia trasy [http zlecenia](#verb) są implementowane `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-498">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="2fa93-499">Gdy wiele atrybutów trasy, które implementują <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> są umieszczane w akcji:</span><span class="sxs-lookup"><span data-stu-id="2fa93-499">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="2fa93-500">Każde ograniczenie akcji łączy się z szablonem trasy zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-500">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="2fa93-501">Korzystanie z wielu tras w akcjach może wydawać się użyteczne i zaawansowane, dlatego lepiej jest zachować podstawową i dobrze zdefiniowaną przestrzeń adresów URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-501">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="2fa93-502">Użyj wielu tras w przypadku akcji **tylko wtedy** , gdy jest to konieczne, na przykład w celu obsługi istniejących klientów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-502">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="2fa93-503">Określanie opcjonalnych parametrów trasy, wartości domyślnych i ograniczeń</span><span class="sxs-lookup"><span data-stu-id="2fa93-503">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="2fa93-504">Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="2fa93-504">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="2fa93-505">W poprzednim kodzie, `[HttpPost("product/{id:int}")]` stosuje ograniczenie trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-505">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="2fa93-506">`ProductsController.ShowProduct`Akcja jest dopasowywana tylko przez ścieżki URL, takie jak `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-506">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="2fa93-507">Część szablonu trasy `{id:int}` ogranicza ten segment tylko do liczb całkowitych.</span><span class="sxs-lookup"><span data-stu-id="2fa93-507">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="2fa93-508">Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-508">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="2fa93-509">Niestandardowe atrybuty trasy przy użyciu IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="2fa93-509">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="2fa93-510">Wszystkie [atrybuty trasy](#rt) implementują <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="2fa93-510">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="2fa93-511">Środowisko uruchomieniowe ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2fa93-511">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="2fa93-512">Wyszukuje atrybuty klas kontrolera i metod akcji podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-512">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="2fa93-513">Używa atrybutów, które implementują `IRouteTemplateProvider` w celu utworzenia początkowego zestawu tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-513">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="2fa93-514">Zaimplementuj `IRouteTemplateProvider` , aby zdefiniować niestandardowe atrybuty trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-514">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="2fa93-515">Każdy `IRouteTemplateProvider` z nich umożliwia definiowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:</span><span class="sxs-lookup"><span data-stu-id="2fa93-515">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="2fa93-516">Poprzednia `Get` Metoda zwraca `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-516">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="2fa93-517">Dostosowywanie tras atrybutów przy użyciu modelu aplikacji</span><span class="sxs-lookup"><span data-stu-id="2fa93-517">Use application model to customize attribute routes</span></span>

<span data-ttu-id="2fa93-518">Model aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2fa93-518">The application model:</span></span>

* <span data-ttu-id="2fa93-519">Jest modelem obiektu utworzonym podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-519">Is an object model created at startup.</span></span>
* <span data-ttu-id="2fa93-520">Zawiera wszystkie metadane używane przez ASP.NET Core do kierowania i wykonywania akcji w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-520">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="2fa93-521">Model aplikacji zawiera wszystkie dane zebrane z atrybutów tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-521">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="2fa93-522">Dane z atrybutów trasy są udostępniane przez `IRouteTemplateProvider` implementację.</span><span class="sxs-lookup"><span data-stu-id="2fa93-522">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="2fa93-523">Obowiązujący</span><span class="sxs-lookup"><span data-stu-id="2fa93-523">Conventions:</span></span>

* <span data-ttu-id="2fa93-524">Można napisać, aby zmodyfikować model aplikacji, aby dostosować sposób zachowania routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-524">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="2fa93-525">Są odczytywane podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-525">Are read at app startup.</span></span>

<span data-ttu-id="2fa93-526">W tej sekcji przedstawiono podstawowy przykład dostosowywania routingu przy użyciu modelu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-526">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="2fa93-527">Poniższy kod sprawia, że trasy są w przybliżeniu zgodne ze strukturą folderów projektu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-527">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="2fa93-528">Poniższy kod uniemożliwia `namespace` stosowanie Konwencji do kontrolerów, które są kierowane przez atrybut:</span><span class="sxs-lookup"><span data-stu-id="2fa93-528">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="2fa93-529">Na przykład następujący kontroler nie używa `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-529">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="2fa93-530">`NamespaceRoutingConvention.Apply`Metoda:</span><span class="sxs-lookup"><span data-stu-id="2fa93-530">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="2fa93-531">Nie robi nic, Jeśli kontroler ma przypisany atrybut.</span><span class="sxs-lookup"><span data-stu-id="2fa93-531">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="2fa93-532">Ustawia szablon szablonu oparty na `namespace` , z `namespace` usuniętym podstawowym.</span><span class="sxs-lookup"><span data-stu-id="2fa93-532">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="2fa93-533">`NamespaceRoutingConvention`Można zastosować w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-533">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="2fa93-534">Rozważmy na przykład następujący kontroler:</span><span class="sxs-lookup"><span data-stu-id="2fa93-534">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="2fa93-535">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-535">In the preceding code:</span></span>

* <span data-ttu-id="2fa93-536">Podstawą `namespace` jest `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-536">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="2fa93-537">Pełna nazwa poprzedniego kontrolera to `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-537">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="2fa93-538">`NamespaceRoutingConvention`Ustawia szablon controllers na `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-538">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="2fa93-539">`NamespaceRoutingConvention`Można również zastosować jako atrybut na kontrolerze:</span><span class="sxs-lookup"><span data-stu-id="2fa93-539">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="2fa93-540">Routing mieszany: Routing atrybutów a konwencjonalny Routing</span><span class="sxs-lookup"><span data-stu-id="2fa93-540">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="2fa93-541">Aplikacje ASP.NET Core mogą łączyć użycie konwencjonalnych routingu i routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-541">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="2fa93-542">Typowym zastosowaniem są trasy konwencjonalne dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.</span><span class="sxs-lookup"><span data-stu-id="2fa93-542">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="2fa93-543">Akcje są albo podkierowane do Konwencji lub kierowane przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="2fa93-543">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="2fa93-544">Umieszczenie trasy na kontrolerze lub akcja powoduje, że atrybut jest kierowany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-544">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="2fa93-545">Akcje definiujące trasy atrybutów nie mogą być osiągane za pomocą konwencjonalnych tras i vice versa.</span><span class="sxs-lookup"><span data-stu-id="2fa93-545">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="2fa93-546">**Każdy** atrybut trasy na kontrolerze powoduje kierowanie **wszystkich** akcji w atrybucie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-546">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="2fa93-547">Routing atrybutów i Routing konwencjonalny używają tego samego aparatu routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-547">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="2fa93-548">Generowanie adresów URL i wartości otoczenia</span><span class="sxs-lookup"><span data-stu-id="2fa93-548">URL Generation and ambient values</span></span>

<span data-ttu-id="2fa93-549">Aplikacje mogą używać funkcji generowania adresów URL routingu do generowania linków URL do akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-549">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="2fa93-550">Generowanie adresów URL eliminuje adresy URL zakodowana, co sprawia, że kod jest bardziej niezawodny i konserwowany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-550">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="2fa93-551">Ta sekcja koncentruje się na funkcjach generowania adresów URL udostępnianych przez MVC i obejmuje jedynie podstawowe informacje na temat sposobu działania generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-551">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="2fa93-552">Aby uzyskać szczegółowy opis generowania adresów URL, zobacz temat [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-552">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="2fa93-553"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Interfejs jest podstawowym elementem infrastruktury między MVC i Routing na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-553">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="2fa93-554">Wystąpienie `IUrlHelper` jest dostępne za pomocą `Url` właściwości w kontrolerach, widokach i składnikach widoku.</span><span class="sxs-lookup"><span data-stu-id="2fa93-554">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="2fa93-555">W poniższym przykładzie `IUrlHelper` interfejs jest używany przez `Controller.Url` Właściwość w celu WYGENEROWANIA adresu URL dla innej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-555">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="2fa93-556">Jeśli aplikacja używa domyślnej trasy konwencjonalnej, wartość `url` zmiennej jest ciągiem ścieżki URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-556">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="2fa93-557">Ta ścieżka URL jest tworzona przez połączenie za pomocą routingu:</span><span class="sxs-lookup"><span data-stu-id="2fa93-557">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="2fa93-558">Wartości trasy z bieżącego żądania, które są nazywane **wartościami otoczenia**.</span><span class="sxs-lookup"><span data-stu-id="2fa93-558">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="2fa93-559">Wartości przesyłane do `Url.Action` i podstawiające te wartości do szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="2fa93-559">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="2fa93-560">Każdy parametr trasy w szablonie trasy ma swoją wartość zastępowaną przez pasujące nazwy wartościami i wartościami otoczenia.</span><span class="sxs-lookup"><span data-stu-id="2fa93-560">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="2fa93-561">Parametr trasy, który nie ma wartości, może:</span><span class="sxs-lookup"><span data-stu-id="2fa93-561">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="2fa93-562">Użyj wartości domyślnej, jeśli ma ją.</span><span class="sxs-lookup"><span data-stu-id="2fa93-562">Use a default value if it has one.</span></span>
* <span data-ttu-id="2fa93-563">Można pominąć, jeśli jest to opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-563">Be skipped if it's optional.</span></span> <span data-ttu-id="2fa93-564">Na przykład, `id` z szablonu trasy `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-564">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="2fa93-565">Generowanie adresu URL kończy się niepowodzeniem, jeśli którykolwiek z wymaganych parametrów trasy nie ma odpowiadającej mu wartości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-565">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="2fa93-566">Jeśli generowanie adresów URL kończy się niepowodzeniem dla trasy, kolejna trasa zostanie ponowiona do momentu przetworzenia wszystkich tras lub znalezienia dopasowania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-566">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="2fa93-567">W poprzednim przykładzie `Url.Action` przyjęto założenie [konwencjonalnego routingu](#cr).</span><span class="sxs-lookup"><span data-stu-id="2fa93-567">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="2fa93-568">Generowanie adresów URL działa podobnie jak w przypadku [routingu atrybutów](#ar), chociaż koncepcje różnią się.</span><span class="sxs-lookup"><span data-stu-id="2fa93-568">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="2fa93-569">Z routingiem konwencjonalnym:</span><span class="sxs-lookup"><span data-stu-id="2fa93-569">With conventional routing:</span></span>

* <span data-ttu-id="2fa93-570">Wartości trasy służą do rozszerzania szablonu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-570">The route values are used to expand a template.</span></span>
* <span data-ttu-id="2fa93-571">Wartości trasy dla `controller` i `action` zwykle pojawiają się w tym szablonie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-571">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="2fa93-572">Dzieje się tak, ponieważ adresy URL dopasowane przez Routing są zgodne z Konwencją.</span><span class="sxs-lookup"><span data-stu-id="2fa93-572">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="2fa93-573">W poniższym przykładzie zastosowano Routing atrybutów:</span><span class="sxs-lookup"><span data-stu-id="2fa93-573">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="2fa93-574">`Source`Akcja w poprzednim kodzie generuje `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-574">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="2fa93-575"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>został dodany w ASP.NET Core 3,0 jako alternatywa dla `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-575"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="2fa93-576">`LinkGenerator`oferuje podobne, ale bardziej elastyczne funkcje.</span><span class="sxs-lookup"><span data-stu-id="2fa93-576">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="2fa93-577">Każda metoda w systemie `IUrlHelper` ma również odpowiednią rodzinę metod `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-577">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="2fa93-578">Generowanie adresów URL według nazwy akcji</span><span class="sxs-lookup"><span data-stu-id="2fa93-578">Generating URLs by action name</span></span>

<span data-ttu-id="2fa93-579">Wartość [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)i wszystkie powiązane przeciążenia są przeznaczone do generowania docelowego punktu końcowego przez określenie nazwy kontrolera i nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-579">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="2fa93-580">W przypadku używania `Url.Action` , bieżąca wartość trasy dla `controller` i `action` jest udostępniana przez środowisko uruchomieniowe:</span><span class="sxs-lookup"><span data-stu-id="2fa93-580">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="2fa93-581">Wartość `controller` i `action` jest częścią zarówno [wartości otoczenia](#ambient) , jak i wartości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-581">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="2fa93-582">Metoda `Url.Action` zawsze używa bieżących wartości `action` i `controller` i generuje ścieżkę URL, która jest przesyłana do bieżącej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-582">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="2fa93-583">Funkcja routingu próbuje użyć wartości w otoczeniu wartości, aby podać informacje, które nie zostały dostarczone podczas generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-583">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="2fa93-584">Rozważ użycie trasy podobnej do `{a}/{b}/{c}/{d}` wartości otoczenia `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-584">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="2fa93-585">Routing ma wystarczającą ilość informacji do wygenerowania adresu URL bez dodatkowych wartości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-585">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="2fa93-586">Routing ma wystarczającą ilość informacji, ponieważ wszystkie parametry tras mają wartość.</span><span class="sxs-lookup"><span data-stu-id="2fa93-586">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="2fa93-587">Jeśli wartość `{ d = Donovan }` zostanie dodana:</span><span class="sxs-lookup"><span data-stu-id="2fa93-587">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="2fa93-588">Wartość `{ d = David }` jest ignorowana.</span><span class="sxs-lookup"><span data-stu-id="2fa93-588">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="2fa93-589">Wygenerowana Ścieżka adresu URL to `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-589">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="2fa93-590">**Ostrzeżenie**: ścieżki URL są hierarchiczne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-590">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="2fa93-591">W poprzednim przykładzie, jeśli wartość `{ c = Cheryl }` jest dodawana:</span><span class="sxs-lookup"><span data-stu-id="2fa93-591">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="2fa93-592">Obie wartości `{ c = Carol, d = David }` są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="2fa93-592">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="2fa93-593">Nie jest już dostępna wartość `d` i generowanie adresu URL nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="2fa93-593">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="2fa93-594">Wymagane wartości `c` i `d` muszą być określone w celu WYGENEROWANIA adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-594">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="2fa93-595">Prawdopodobnie wystąpił problem z domyślną trasą `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-595">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="2fa93-596">Ten problem występuje rzadko `Url.Action` , ponieważ zawsze jawnie określa `controller` `action` wartość i.</span><span class="sxs-lookup"><span data-stu-id="2fa93-596">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="2fa93-597">Kilka przeciążeń [adresu URL. akcja](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) przyjmuje obiekt wartości trasy, aby podać wartości parametrów trasy innych niż `controller` i `action` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-597">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="2fa93-598">Obiekt wartości trasy jest często używany z `id` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-598">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="2fa93-599">Na przykład `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="2fa93-599">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="2fa93-600">Obiekt wartości trasy:</span><span class="sxs-lookup"><span data-stu-id="2fa93-600">The route values object:</span></span>

* <span data-ttu-id="2fa93-601">Według Konwencji jest zwykle obiektem typu anonimowego.</span><span class="sxs-lookup"><span data-stu-id="2fa93-601">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="2fa93-602">Może to być `IDictionary<>` lub [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="2fa93-602">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="2fa93-603">Wszystkie dodatkowe wartości trasy, które nie pasują do parametrów trasy, są umieszczane w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-603">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="2fa93-604">Poprzedni kod generuje `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-604">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="2fa93-605">Poniższy kod generuje bezwzględny adres URL:</span><span class="sxs-lookup"><span data-stu-id="2fa93-605">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="2fa93-606">Aby utworzyć bezwzględny adres URL, użyj jednego z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="2fa93-606">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="2fa93-607">Przeciążenie, które akceptuje `protocol` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-607">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="2fa93-608">Na przykład poprzedzający kod.</span><span class="sxs-lookup"><span data-stu-id="2fa93-608">For example, the preceding code.</span></span>
* <span data-ttu-id="2fa93-609">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), który domyślnie generuje bezwzględne identyfikatory URI.</span><span class="sxs-lookup"><span data-stu-id="2fa93-609">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="2fa93-610">Generuj adresy URL według trasy</span><span class="sxs-lookup"><span data-stu-id="2fa93-610">Generate URLs by route</span></span>

<span data-ttu-id="2fa93-611">Poprzedni kod wykazał wygenerowanie adresu URL przez przekazanie go do kontrolera i nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-611">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="2fa93-612">`IUrlHelper`zapewnia także rodzinę [adresów URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-612">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="2fa93-613">Te metody są podobne do [adresu URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nie kopiują bieżących wartości `action` i `controller` do wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-613">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="2fa93-614">Najczęstsze użycie `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-614">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="2fa93-615">Określa nazwę trasy do wygenerowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-615">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="2fa93-616">Zwykle nie określa kontrolera ani nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-616">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="2fa93-617">Następujący Razor plik generuje link HTML do `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-617">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="2fa93-618">Generuj adresy URL w kodzie HTML iRazor</span><span class="sxs-lookup"><span data-stu-id="2fa93-618">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="2fa93-619"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>udostępnia <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) oraz [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) do wygenerowania `<form>` i `<a>` elementów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-619"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="2fa93-620">Metody te używają metody [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) do generowania adresu URL i akceptują podobne argumenty.</span><span class="sxs-lookup"><span data-stu-id="2fa93-620">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="2fa93-621">`Url.RouteUrl`Pomocników dla programu `HtmlHelper` mają `Html.BeginRouteForm` `Html.RouteLink` podobną funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="2fa93-621">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="2fa93-622">TagHelpers Generuj adresy URL za pomocą `form` TagHelper i `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="2fa93-622">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="2fa93-623">Oba te zastosowania `IUrlHelper` dla ich implementacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-623">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="2fa93-624">Aby uzyskać więcej informacji, zobacz [pomocników tagów w formularzach](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-624">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="2fa93-625">W widokach `IUrlHelper` można korzystać z `Url` właściwości dla dowolnej generacji adresów URL ad hoc, która nie jest objęta powyższym.</span><span class="sxs-lookup"><span data-stu-id="2fa93-625">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="2fa93-626">Generowanie adresów URL w wynikach akcji</span><span class="sxs-lookup"><span data-stu-id="2fa93-626">URL generation in Action Results</span></span>

<span data-ttu-id="2fa93-627">Poprzednie przykłady pokazano przy użyciu `IUrlHelper` w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="2fa93-627">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="2fa93-628">Najczęstszym sposobem użycia na kontrolerze jest wygenerowanie adresu URL jako części wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-628">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="2fa93-629"><xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> Klasy bazowe i zapewniają wygodne metody dla wyników akcji, które odwołują się do innej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-629">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="2fa93-630">Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika:</span><span class="sxs-lookup"><span data-stu-id="2fa93-630">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="2fa93-631">Wynikiem akcji są metody fabryki, takie jak <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> i, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> postępuj zgodnie z podobnym wzorcem do metod w `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-631">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="2fa93-632">Specjalny przypadek dla dedykowanych tras konwencjonalnych</span><span class="sxs-lookup"><span data-stu-id="2fa93-632">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="2fa93-633">Funkcja [routingu konwencjonalnego](#cr) może używać specjalnego rodzaju definicji trasy o nazwie [dedykowanej, konwencjonalnej trasy](#dcr).</span><span class="sxs-lookup"><span data-stu-id="2fa93-633">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="2fa93-634">W poniższym przykładzie trasa o nazwie `blog` jest dedykowaną konwencjonalną trasą:</span><span class="sxs-lookup"><span data-stu-id="2fa93-634">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="2fa93-635">Przy użyciu powyższych definicji trasy program `Url.Action("Index", "Home")` generuje ścieżkę URL `/` przy użyciu `default` trasy, ale dlaczego?</span><span class="sxs-lookup"><span data-stu-id="2fa93-635">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="2fa93-636">Możliwe jest odpuszczenie, że wartości trasy `{ controller = Home, action = Index }` byłyby wystarczające do wygenerowania adresu URL przy użyciu `blog` , a wynikiem będzie `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-636">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="2fa93-637">[Dedykowane konwencjonalne trasy](#dcr) polegają na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiadającego parametru trasy, który uniemożliwia zbyt [zachłanne](xref:fundamentals/routing#greedy) trasy z generowaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-637">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="2fa93-638">W takim przypadku wartości domyślne to `{ controller = Blog, action = Article }` , a ani nie `controller` `action` pojawiają się jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-638">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="2fa93-639">Gdy Routing wykonuje generowanie adresów URL, podane wartości muszą być zgodne z wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="2fa93-639">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="2fa93-640">Generowanie adresu URL `blog` nie powiodło się, ponieważ wartości `{ controller = Home, action = Index }` nie są zgodne `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-640">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="2fa93-641">Następnie usługa routingu wraca do wypróbowania `default` , która się powiedzie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-641">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="2fa93-642">Obszary</span><span class="sxs-lookup"><span data-stu-id="2fa93-642">Areas</span></span>

<span data-ttu-id="2fa93-643">[Obszary](xref:mvc/controllers/areas) są funkcją MVC służącą do organizowania powiązanych funkcji w grupie jako oddzielnej:</span><span class="sxs-lookup"><span data-stu-id="2fa93-643">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="2fa93-644">Przestrzeń nazw routingu dla akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-644">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="2fa93-645">Struktura folderów dla widoków.</span><span class="sxs-lookup"><span data-stu-id="2fa93-645">Folder structure for views.</span></span>

<span data-ttu-id="2fa93-646">Użycie obszarów umożliwia aplikacji posiadanie wielu kontrolerów o tej samej nazwie, o ile mają one różne obszary.</span><span class="sxs-lookup"><span data-stu-id="2fa93-646">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="2fa93-647">Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie kolejnego parametru trasy `area` do `controller` i `action` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-647">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="2fa93-648">W tej sekcji omówiono sposób, w jaki Routing współdziała z obszarami.</span><span class="sxs-lookup"><span data-stu-id="2fa93-648">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="2fa93-649">Zobacz [obszary](xref:mvc/controllers/areas) , aby uzyskać szczegółowe informacje na temat sposobu używania obszarów w widokach.</span><span class="sxs-lookup"><span data-stu-id="2fa93-649">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="2fa93-650">Poniższy przykład konfiguruje MVC do używania domyślnej trasy konwencjonalnej i `area` trasy dla `area` nazwanego `Blog` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-650">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="2fa93-651">W poprzednim kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> jest wywoływana w celu utworzenia `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-651">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="2fa93-652">Drugi parametr, `"Blog"` , jest nazwą obszaru.</span><span class="sxs-lookup"><span data-stu-id="2fa93-652">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="2fa93-653">W przypadku dopasowania ścieżki URL podobnej `/Manage/Users/AddUser` do `"blog_route"` trasy generuje wartości trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-653">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="2fa93-654">`area`Wartość trasy jest generowana przez wartość domyślną dla `area` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-654">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="2fa93-655">Trasa utworzona przez `MapAreaControllerRoute` jest równoważna następującym:</span><span class="sxs-lookup"><span data-stu-id="2fa93-655">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="2fa93-656">`MapAreaControllerRoute`tworzy trasę przy użyciu wartości domyślnej i ograniczenia dotyczącej `area` użycia podanej nazwy obszaru, w tym przypadku `Blog` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-656">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="2fa93-657">Wartość domyślna zapewnia, że trasa zawsze produkuje `{ area = Blog, ... }` , ograniczenie wymaga wartości `{ area = Blog, ... }` dla generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-657">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="2fa93-658">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-658">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2fa93-659">Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczone wcześniej, ponieważ są bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="2fa93-659">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2fa93-660">Korzystając z powyższego przykładu, wartości trasy `{ area = Blog, controller = Users, action = AddUser }` są zgodne z następującą akcją:</span><span class="sxs-lookup"><span data-stu-id="2fa93-660">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="2fa93-661">Atrybut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) wskazuje, co oznacza kontroler w ramach obszaru.</span><span class="sxs-lookup"><span data-stu-id="2fa93-661">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="2fa93-662">Ten kontroler znajduje się w `Blog` obszarze.</span><span class="sxs-lookup"><span data-stu-id="2fa93-662">This controller is in the `Blog` area.</span></span> <span data-ttu-id="2fa93-663">Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru i **nie** są zgodne, gdy `area` wartość trasy jest dostarczana przez Routing.</span><span class="sxs-lookup"><span data-stu-id="2fa93-663">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="2fa93-664">W poniższym przykładzie tylko pierwszy kontroler może być zgodny z wartościami trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-664">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="2fa93-665">Przestrzeń nazw każdego kontrolera jest pokazana w tym miejscu na potrzeby kompletności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-665">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="2fa93-666">Jeśli powyższe kontrolery używają tego samego obszaru nazw, wygenerowany zostanie błąd kompilatora.</span><span class="sxs-lookup"><span data-stu-id="2fa93-666">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="2fa93-667">Przestrzenie nazw klas nie mają wpływu na Routing MVC.</span><span class="sxs-lookup"><span data-stu-id="2fa93-667">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="2fa93-668">Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko wtedy, gdy ich nazwa obszaru jest podana przez `area` wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-668">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="2fa93-669">Trzeci kontroler nie jest członkiem żadnego obszaru i można go dopasować tylko wtedy, gdy nie ma żadnej wartości parametru `area` Routing.</span><span class="sxs-lookup"><span data-stu-id="2fa93-669">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="2fa93-670">W warunkach pasujących do *nie ma żadnej*wartości, brak `area` wartości jest taka sama jak wartość `area` null lub pusty ciąg.</span><span class="sxs-lookup"><span data-stu-id="2fa93-670">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="2fa93-671">Podczas wykonywania akcji wewnątrz obszaru wartość trasy dla `area` jest dostępna jako [wartość otoczenia](#ambient) dla routingu do użycia na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-671">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="2fa93-672">Oznacza to, że domyślnie obszary programu *Sticky Notes* mają być używane do generowania adresów URL, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-672">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="2fa93-673">Poniższy kod generuje adres URL `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-673">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="2fa93-674">Definicja akcji</span><span class="sxs-lookup"><span data-stu-id="2fa93-674">Action definition</span></span>

<span data-ttu-id="2fa93-675">Metody publiczne na kontrolerze, z wyjątkiem tych z atrybutem nie będącym [akcją](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , są akcjami.</span><span class="sxs-lookup"><span data-stu-id="2fa93-675">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="2fa93-676">Przykładowy kod</span><span class="sxs-lookup"><span data-stu-id="2fa93-676">Sample code</span></span>

 * <span data-ttu-id="2fa93-677">Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) jest dołączana do [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) i służy do wyświetlania informacji o routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-677">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="2fa93-678">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fa93-678">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2fa93-679">ASP.NET Core MVC używa [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) routingu, aby dopasować adresy URL żądań przychodzących i zmapować je na akcje.</span><span class="sxs-lookup"><span data-stu-id="2fa93-679">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="2fa93-680">Trasy są zdefiniowane w kodzie startowym lub atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-680">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="2fa93-681">Trasy opisują sposób dopasowywania ścieżek adresów URL do akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-681">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="2fa93-682">Trasy są również używane do generowania adresów URL (dla linków) wysyłanych w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="2fa93-682">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="2fa93-683">Akcje są albo podkierowane do Konwencji lub kierowane przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="2fa93-683">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="2fa93-684">Umieszczenie trasy na kontrolerze lub akcja powoduje, że atrybut jest kierowany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-684">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="2fa93-685">Aby uzyskać więcej informacji, zobacz [Routing mieszany](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-685">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="2fa93-686">Ten dokument wyjaśnia interakcje między MVC i routingiem oraz sposób, w jaki typowe aplikacje MVC używają funkcji routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-686">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="2fa93-687">Zobacz [Routing](xref:fundamentals/routing) , aby uzyskać szczegółowe informacje na temat routingu zaawansowanego.</span><span class="sxs-lookup"><span data-stu-id="2fa93-687">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="2fa93-688">Konfigurowanie oprogramowania pośredniczącego routingu</span><span class="sxs-lookup"><span data-stu-id="2fa93-688">Setting up Routing Middleware</span></span>

<span data-ttu-id="2fa93-689">W metodzie *konfigurowania* może zostać wyświetlony kod podobny do:</span><span class="sxs-lookup"><span data-stu-id="2fa93-689">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2fa93-690">Wewnątrz wywołania do `UseMvc` , służy `MapRoute` do tworzenia pojedynczej trasy, którą będziemy odnosić jako `default` trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-690">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="2fa93-691">Większość aplikacji MVC będzie używać trasy z szablonem podobnym do `default` trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-691">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="2fa93-692">Szablon trasy `"{controller=Home}/{action=Index}/{id?}"` może być zgodny z ścieżką URL `/Products/Details/5` , tak jak i będzie wyodrębniał wartości trasy `{ controller = Products, action = Details, id = 5 }` przez tokenizowanie ścieżki.</span><span class="sxs-lookup"><span data-stu-id="2fa93-692">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="2fa93-693">MVC spróbuje zlokalizować kontroler o nazwie `ProductsController` i uruchomić akcję `Details` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-693">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="2fa93-694">Należy zauważyć, że w tym przykładzie powiązanie modelu będzie używać wartości, `id = 5` Aby ustawić `id` parametr `5` podczas wywoływania tej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-694">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="2fa93-695">Aby uzyskać więcej informacji, zobacz [powiązanie modelu](../models/model-binding.md) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-695">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="2fa93-696">Przy użyciu `default` trasy:</span><span class="sxs-lookup"><span data-stu-id="2fa93-696">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2fa93-697">Szablon trasy:</span><span class="sxs-lookup"><span data-stu-id="2fa93-697">The route template:</span></span>

* <span data-ttu-id="2fa93-698">`{controller=Home}`definiuje `Home` jako domyślne`controller`</span><span class="sxs-lookup"><span data-stu-id="2fa93-698">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="2fa93-699">`{action=Index}`definiuje `Index` jako domyślne`action`</span><span class="sxs-lookup"><span data-stu-id="2fa93-699">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="2fa93-700">`{id?}`definiuje `id` jako opcjonalne</span><span class="sxs-lookup"><span data-stu-id="2fa93-700">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="2fa93-701">Domyślne i opcjonalne parametry trasy nie muszą być obecne w ścieżce URL dla dopasowania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-701">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="2fa93-702">Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-702">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="2fa93-703">`"{controller=Home}/{action=Index}/{id?}"`może być zgodna ze ścieżką URL `/` i będzie generować wartości trasy `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-703">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="2fa93-704">Wartości dla `controller` i `action` używają wartości domyślnych, `id` nie tworzy wartości, ponieważ w ścieżce URL nie ma odpowiedniego segmentu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-704">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="2fa93-705">Aby można było wybrać akcję i, MVC będzie używać tych wartości tras `HomeController` `Index` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-705">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="2fa93-706">Przy użyciu tej definicji kontrolera i szablonu trasy `HomeController.Index` Akcja będzie wykonywana dla dowolnej z następujących ścieżek URL:</span><span class="sxs-lookup"><span data-stu-id="2fa93-706">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="2fa93-707">Wygodna metoda `UseMvcWithDefaultRoute` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-707">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="2fa93-708">Może służyć do zastępowania:</span><span class="sxs-lookup"><span data-stu-id="2fa93-708">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2fa93-709">`UseMvc`i `UseMvcWithDefaultRoute` Dodaj wystąpienie `RouterMiddleware` do potoku programu pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="2fa93-709">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="2fa93-710">MVC nie działa bezpośrednio w oprogramowaniu pośredniczącym i używa routingu do obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="2fa93-710">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="2fa93-711">MVC jest połączony z trasami za pomocą wystąpienia `MvcRouteHandler` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-711">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="2fa93-712">Kod w programie `UseMvc` jest podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="2fa93-712">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="2fa93-713">`UseMvc`nie definiuje bezpośrednio żadnych tras, dodaje symbol zastępczy do kolekcji tras dla `attribute` trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-713">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="2fa93-714">Przeciążenie `UseMvc(Action<IRouteBuilder>)` umożliwia dodanie własnych tras, a także obsługuje routing atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-714">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="2fa93-715">`UseMvc`i wszystkie jego odmiany dodają symbol zastępczy dla atrybutu trasy — atrybut jest zawsze dostępny niezależnie od sposobu konfigurowania `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-715">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="2fa93-716">`UseMvcWithDefaultRoute`Definiuje domyślną trasę i obsługuje routing atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-716">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="2fa93-717">Sekcja [Routing atrybutów](#attribute-routing-ref-label) zawiera więcej szczegółów dotyczących routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-717">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="2fa93-718">Routing konwencjonalny</span><span class="sxs-lookup"><span data-stu-id="2fa93-718">Conventional routing</span></span>

<span data-ttu-id="2fa93-719">`default`Trasa:</span><span class="sxs-lookup"><span data-stu-id="2fa93-719">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2fa93-720">Poprzedni kod jest przykładem konwencjonalnego routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-720">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="2fa93-721">Ten styl jest nazywany routingiem konwencjonalnym, ponieważ ustanawia *Konwencję* dla ścieżek adresów URL:</span><span class="sxs-lookup"><span data-stu-id="2fa93-721">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="2fa93-722">Pierwszy segment ścieżki jest mapowany na nazwę kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-722">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="2fa93-723">Druga mapowanie na nazwę akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-723">The second maps to the action name.</span></span>
* <span data-ttu-id="2fa93-724">Trzeci segment jest używany opcjonalnie `id` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-724">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="2fa93-725">`id`mapuje do jednostki modelu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-725">`id` maps to a model entity.</span></span>

<span data-ttu-id="2fa93-726">Przy użyciu tej `default` trasy ścieżka URL jest `/Products/List` mapowana na `ProductsController.List` akcję i jest `/Blog/Article/17` mapowana na `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-726">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="2fa93-727">To mapowanie jest oparte **tylko** na nazwach kontrolera i akcji, a nie na podstawie przestrzeni nazw, lokalizacji plików źródłowych ani parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="2fa93-727">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="2fa93-728">Użycie konwencjonalnego routingu z domyślną trasą umożliwia szybkie Kompilowanie aplikacji bez konieczności podawania nowego wzorca adresu URL dla każdej zdefiniowanej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-728">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="2fa93-729">W przypadku aplikacji z akcjami w stylu CRUD zachowanie spójności adresów URL na kontrolerach może pomóc uprościć kod i uczynić interfejs użytkownika bardziej przewidywalny.</span><span class="sxs-lookup"><span data-stu-id="2fa93-729">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="2fa93-730">`id`Jest zdefiniowany jako opcjonalny przez szablon trasy, co oznacza, że akcje mogą być wykonywane bez identyfikatora dostarczonego jako część adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-730">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="2fa93-731">Typowo, co się stanie, jeśli zostanie `id` pominięty z adresu URL, to zostanie ono ustawione na `0` podstawie powiązania modelu, a w rezultacie nie zostanie znaleziona żadna jednostka w dopasowaniu do bazy danych `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-731">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="2fa93-732">Routing atrybutu może dać szczegółowy formant, który ma być wymagany dla niektórych akcji, a nie dla innych.</span><span class="sxs-lookup"><span data-stu-id="2fa93-732">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="2fa93-733">Zgodnie z Konwencją dokumentacja będzie zawierać opcjonalne parametry, takie jak, `id` gdy prawdopodobnie będą widoczne w prawidłowym użyciu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-733">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="2fa93-734">Wiele tras</span><span class="sxs-lookup"><span data-stu-id="2fa93-734">Multiple routes</span></span>

<span data-ttu-id="2fa93-735">Można dodać wiele tras wewnątrz `UseMvc` , dodając więcej wywołań do `MapRoute` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-735">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="2fa93-736">Dzięki temu można zdefiniować wiele Konwencji lub dodać trasy konwencjonalne, które są przeznaczone dla konkretnej akcji, na przykład:</span><span class="sxs-lookup"><span data-stu-id="2fa93-736">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2fa93-737">`blog`Trasa w tym miejscu jest *dedykowaną tradycyjną trasą*, co oznacza, że używa systemu routingu konwencjonalnego, ale jest przeznaczona dla konkretnej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-737">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="2fa93-738">Ponieważ `controller` i `action` nie pojawiają się w szablonie trasy jako parametry, mogą mieć tylko wartości domyślne i w związku z tym trasa będzie zawsze mapowana na akcję `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-738">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="2fa93-739">Trasy w kolekcji tras są uporządkowane i będą przetwarzane w kolejności, w jakiej zostały dodane.</span><span class="sxs-lookup"><span data-stu-id="2fa93-739">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="2fa93-740">W tym przykładzie `blog` zostanie podjęta próba trasy przed `default` trasą.</span><span class="sxs-lookup"><span data-stu-id="2fa93-740">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="2fa93-741">*Dedykowane konwencjonalne trasy* często korzystają z parametrów trasy **catch-all** , takich jak `{*article}` przechwytywanie pozostałej części ścieżki URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-741">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="2fa93-742">Może to spowodować, że trasa "zbyt zachłanne" oznacza, że pasuje do adresów URL, które mają być dopasowane przez inne trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-742">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="2fa93-743">Umieść trasy "zachłanne" później w tabeli tras, aby rozwiązać ten problem.</span><span class="sxs-lookup"><span data-stu-id="2fa93-743">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="2fa93-744">Opcja rezerwowa</span><span class="sxs-lookup"><span data-stu-id="2fa93-744">Fallback</span></span>

<span data-ttu-id="2fa93-745">W ramach przetwarzania żądań MVC sprawdzi, czy wartości trasy mogą być używane do znajdowania kontrolera i akcji w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-745">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="2fa93-746">Jeśli wartości trasy nie są zgodne z akcją, trasa nie jest uważana za dopasowanie i zostanie podjęta kolejna trasa.</span><span class="sxs-lookup"><span data-stu-id="2fa93-746">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="2fa93-747">Jest to nazywane *Fallback*i ma na celu uproszczenie przypadków, w których trasy konwencjonalne nakładają się na siebie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-747">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="2fa93-748">Niejednoznaczne akcje</span><span class="sxs-lookup"><span data-stu-id="2fa93-748">Disambiguating actions</span></span>

<span data-ttu-id="2fa93-749">Gdy dwie akcje są zgodne z routingiem, MVC musi odróżnić się, aby wybrać najlepszy kandydat lub w przeciwnym razie zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="2fa93-749">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="2fa93-750">Przykład:</span><span class="sxs-lookup"><span data-stu-id="2fa93-750">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="2fa93-751">Ten kontroler definiuje dwie akcje, które byłyby zgodne ze ścieżką URL `/Products/Edit/17` i danymi tras `{ controller = Products, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-751">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="2fa93-752">Jest to typowy wzorzec dla kontrolerów MVC, gdzie `Edit(int)` pokazuje formularz służący do edytowania produktu i `Edit(int, Product)` przetwarza opublikowany formularz.</span><span class="sxs-lookup"><span data-stu-id="2fa93-752">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="2fa93-753">Aby ten możliwy składnik MVC musiał wybrać, `Edit(int, Product)` gdy żądanie jest http `POST` i `Edit(int)` gdy zlecenie http jest inne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-753">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="2fa93-754">`HttpPostAttribute`( `[HttpPost]` ) Jest implementacją programu `IActionConstraint` , która będzie zezwalać na wybór akcji tylko wtedy, gdy czasownik http to `POST` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-754">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="2fa93-755">Obecność elementu sprawia, że `IActionConstraint` `Edit(int, Product)` lepszym rozwiązaniem jest dopasowanie `Edit(int)` , więc `Edit(int, Product)` zostanie ono najpierw ponowione.</span><span class="sxs-lookup"><span data-stu-id="2fa93-755">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="2fa93-756">Musisz tylko pisać `IActionConstraint` implementacje niestandardowe w wyspecjalizowanych scenariuszach, ale ważne jest, aby zrozumieć rolę atrybutów, takich jak `HttpPostAttribute` atrybuty podobne do innych czasowników HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fa93-756">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="2fa93-757">W przypadku routingu konwencjonalnego typowe dla akcji używanie tej samej nazwy akcji, gdy są one częścią `show form -> submit form` przepływu pracy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-757">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="2fa93-758">Wygoda tego wzorca stanie się bardziej oczywista po przejrzeniu sekcji [zrozumienie IActionConstraint](#understanding-iactionconstraint) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-758">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="2fa93-759">Jeśli wiele pasujących tras i MVC nie mogą znaleźć "najlepszej" trasy, spowoduje to zgłoszenie `AmbiguousActionException` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-759">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="2fa93-760">Nazwy tras</span><span class="sxs-lookup"><span data-stu-id="2fa93-760">Route names</span></span>

<span data-ttu-id="2fa93-761">Ciągi `"blog"` i `"default"` w poniższych przykładach są nazwami tras:</span><span class="sxs-lookup"><span data-stu-id="2fa93-761">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2fa93-762">Nazwy tras nadaj logicznej nazwie trasy, aby nazwana trasa mogła być używana do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-762">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="2fa93-763">Znacznie upraszcza to tworzenie adresów URL, gdy porządkowanie tras może spowodować skomplikowane generowanie adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-763">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="2fa93-764">Nazwy tras muszą być unikatowe w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-764">Route names must be unique application-wide.</span></span>

<span data-ttu-id="2fa93-765">Nazwy tras nie mają wpływu na Dopasowywanie adresów URL ani obsługę żądań; są one używane tylko do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-765">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="2fa93-766">[Routing](xref:fundamentals/routing) zawiera bardziej szczegółowe informacje na temat generowania adresów URL, w tym generowanie adresów URL w pomocnikach specyficznych dla MVC.</span><span class="sxs-lookup"><span data-stu-id="2fa93-766">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="2fa93-767">Routing atrybutów</span><span class="sxs-lookup"><span data-stu-id="2fa93-767">Attribute routing</span></span>

<span data-ttu-id="2fa93-768">Funkcja routingu atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-768">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="2fa93-769">W poniższym przykładzie `app.UseMvc();` jest używany w `Configure` metodzie i nie jest przesyłana żadna trasa.</span><span class="sxs-lookup"><span data-stu-id="2fa93-769">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="2fa93-770">`HomeController`Będzie zgodna z zestawem adresów URL podobnym do trasy domyślnej, która `{controller=Home}/{action=Index}/{id?}` byłaby zgodna:</span><span class="sxs-lookup"><span data-stu-id="2fa93-770">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="2fa93-771">`HomeController.Index()`Akcja będzie wykonywana dla dowolnej ścieżki URL `/` , `/Home` lub `/Home/Index` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-771">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="2fa93-772">W tym przykładzie przedstawiono najważniejsze różnice programistyczne między routingiem atrybutów i routingiem konwencjonalnym.</span><span class="sxs-lookup"><span data-stu-id="2fa93-772">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="2fa93-773">Routing atrybutów wymaga więcej danych wejściowych w celu określenia trasy; konwencjonalne trasy domyślne obsługuje trasy bardziej zwięzłie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-773">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="2fa93-774">Jednak Routing atrybutu zezwala na (i wymaga) precyzyjnej kontroli, które szablony tras mają zastosowanie do poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-774">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="2fa93-775">W przypadku routingu atrybutów nazwa kontrolera i nazwy akcji nie odgrywają **żadnej** roli, w której akcja jest zaznaczona.</span><span class="sxs-lookup"><span data-stu-id="2fa93-775">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="2fa93-776">Ten przykład będzie pasował do tych samych adresów URL, co w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-776">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="2fa93-777">Powyższe szablony tras nie definiują parametrów trasy dla `action` , `area` i `controller` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-777">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="2fa93-778">W rzeczywistości te parametry tras są niedozwolone w trasach atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-778">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="2fa93-779">Ponieważ szablon trasy jest już skojarzony z akcją, nie ma sensu analizy nazwy akcji na podstawie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-779">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="2fa93-780">Routing atrybutów z atrybutami http [Verb]</span><span class="sxs-lookup"><span data-stu-id="2fa93-780">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="2fa93-781">Routing atrybutu może również używać `Http[Verb]` atrybutów, takich jak `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-781">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="2fa93-782">Wszystkie te atrybuty mogą akceptować szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-782">All of these attributes can accept a route template.</span></span> <span data-ttu-id="2fa93-783">Ten przykład przedstawia dwie akcje, które pasują do tego samego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="2fa93-783">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="2fa93-784">W przypadku ścieżki URL podobnej do `/products` `ProductsApi.ListProducts` akcji zostanie wykonane, gdy zlecenie http jest `GET` i `ProductsApi.CreateProduct` zostanie wykonane, gdy zlecenie http ma wartość `POST` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-784">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="2fa93-785">Routing atrybutu najpierw jest zgodny z adresem URL względem zestawu szablonów tras zdefiniowanych przez atrybuty trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-785">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="2fa93-786">Po dopasowaniu szablonu trasy `IActionConstraint` ograniczenia są stosowane w celu określenia, które akcje mogą być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="2fa93-786">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="2fa93-787">Podczas kompilowania interfejsu API REST jest to rzadki, że będzie on używany `[Route(...)]` na potrzeby metody akcji, ponieważ akcja akceptuje wszystkie metody http.</span><span class="sxs-lookup"><span data-stu-id="2fa93-787">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="2fa93-788">Lepiej jest użyć bardziej szczegółowych `Http*Verb*Attributes` informacji na temat tego, co obsługuje interfejs API.</span><span class="sxs-lookup"><span data-stu-id="2fa93-788">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="2fa93-789">Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i czasowniki HTTP mapują na określone operacje logiczne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-789">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="2fa93-790">Ponieważ atrybut Route ma zastosowanie do określonej akcji, można łatwo wprowadzić parametry wymagane jako część definicji szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-790">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="2fa93-791">W tym przykładzie `id` jest wymagane jako część ścieżki URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-791">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="2fa93-792">`ProductsApi.GetProduct(int)`Akcja zostanie wykonana dla ścieżki URL, na przykład, `/products/3` ale nie dla ścieżki adresu URL `/products` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-792">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="2fa93-793">Aby uzyskać pełny opis szablonów tras i powiązanych opcji, zobacz [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-793">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="2fa93-794">Nazwa trasy</span><span class="sxs-lookup"><span data-stu-id="2fa93-794">Route Name</span></span>

<span data-ttu-id="2fa93-795">Następujący kod definiuje *nazwę trasy* `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-795">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="2fa93-796">Nazwy tras mogą służyć do generowania adresów URL na podstawie określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-796">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="2fa93-797">Nazwy tras nie mają wpływu na zachowanie routingu w adresie URL i są używane tylko na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-797">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="2fa93-798">Nazwy tras muszą być unikatowe w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-798">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="2fa93-799">Jest to zróżnicowane dla konwencjonalnej *trasy domyślnej*, która definiuje `id` parametr jako opcjonalny ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="2fa93-799">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="2fa93-800">Ta możliwość precyzyjnego określania interfejsów API ma zalety, takich jak umożliwienie `/products` i `/products/5` wysyłanie ich do różnych akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-800">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="2fa93-801">Łączenie tras</span><span class="sxs-lookup"><span data-stu-id="2fa93-801">Combining routes</span></span>

<span data-ttu-id="2fa93-802">Aby mniej powtarzać Routing atrybutów, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-802">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="2fa93-803">Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzone w celu rozesłania szablonów w akcjach.</span><span class="sxs-lookup"><span data-stu-id="2fa93-803">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="2fa93-804">Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w kontrolerze używają routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-804">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="2fa93-805">W tym przykładzie ścieżka adresu URL `/products` może być taka sama `ProductsApi.ListProducts` , a Ścieżka adresu URL `/products/5` może być taka sama `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-805">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="2fa93-806">Obie te akcje pasują tylko do protokołu HTTP `GET` , ponieważ są oznaczone za pomocą `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-806">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="2fa93-807">Szablony tras zastosowane do akcji rozpoczynającej się od `/` lub `~/` nie są połączone z szablonami tras zastosowanymi do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-807">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="2fa93-808">Ten przykład dopasowuje zestaw ścieżek URL podobny do *trasy domyślnej*.</span><span class="sxs-lookup"><span data-stu-id="2fa93-808">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="2fa93-809">Określanie kolejności tras atrybutów</span><span class="sxs-lookup"><span data-stu-id="2fa93-809">Ordering attribute routes</span></span>

<span data-ttu-id="2fa93-810">W przeciwieństwie do konwencjonalnych tras, które są wykonywane w określonej kolejności, routing atrybutu kompiluje drzewo i dopasowuje wszystkie trasy jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-810">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="2fa93-811">Zachowuje się tak, jakby wpisy trasy zostały umieszczone w idealnym porządku; najbardziej konkretne trasy mają możliwość wykonania przed bardziej ogólnymi trasami.</span><span class="sxs-lookup"><span data-stu-id="2fa93-811">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="2fa93-812">Na przykład taka trasa `blog/search/{topic}` jest bardziej szczegółowa niż trasa, taka jak `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-812">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="2fa93-813">Logicznie mówiąc, `blog/search/{topic}` że trasy "uruchomienia" są najpierw domyślnie, ponieważ to jest jedyną rozsądną kolejnością.</span><span class="sxs-lookup"><span data-stu-id="2fa93-813">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="2fa93-814">Przy użyciu konwencjonalnego routingu deweloper jest odpowiedzialny za umieszczanie tras w odpowiedniej kolejności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-814">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="2fa93-815">Trasy atrybutów mogą konfigurować kolejność przy użyciu `Order` właściwości wszystkich atrybutów tras dostarczonych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="2fa93-815">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="2fa93-816">Trasy są przetwarzane zgodnie z rosnącą sortowaniem `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-816">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="2fa93-817">Kolejność domyślna to `0` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-817">The default order is `0`.</span></span> <span data-ttu-id="2fa93-818">Ustawienie trasy przy użyciu `Order = -1` zostanie uruchomione przed trasami, które nie ustawiają kolejności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-818">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="2fa93-819">Ustawienie trasy przy użyciu `Order = 1` zostanie uruchomione po określeniu domyślnej kolejności tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-819">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="2fa93-820">Należy unikać w zależności od `Order` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-820">Avoid depending on `Order`.</span></span> <span data-ttu-id="2fa93-821">Jeśli przestrzeń adresów URL wymaga jawnych wartości kolejności, aby można było prawidłowo kierować trasy, to prawdopodobnie również jest myląca dla klientów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-821">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="2fa93-822">W ogólnym routingu atrybutów wybierz prawidłową trasę z dopasowywaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-822">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="2fa93-823">Jeśli domyślna kolejność generowania adresów URL nie działa, użycie nazwy trasy jako przesłonięcia jest zwykle prostsze niż stosowanie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-823">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="2fa93-824">Strony routingu i routingu kontrolera MVC współdzielą implementację.</span><span class="sxs-lookup"><span data-stu-id="2fa93-824"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="2fa93-825">Informacje o zamówieniu trasy w Razor tematach stron są dostępne na [ Razor stronach trasy i konwencje aplikacji: kolejność trasy](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="2fa93-825">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="2fa93-826">Zastępowanie tokenu w szablonach tras ([Controller], [Action], [obszar])</span><span class="sxs-lookup"><span data-stu-id="2fa93-826">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="2fa93-827">Dla wygody, trasy atrybutu obsługują *Zastępowanie tokenu* przez ujęcie tokenu w nawiasy kwadratowe ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="2fa93-827">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="2fa93-828">Tokeny `[action]` , `[area]` i `[controller]` są zastępowane wartościami nazwy akcji, obszaru i nazwy kontrolera z akcji, w której jest zdefiniowana trasa.</span><span class="sxs-lookup"><span data-stu-id="2fa93-828">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="2fa93-829">W poniższym przykładzie akcje są zgodne ze ścieżkami URL zgodnie z opisem w komentarzach:</span><span class="sxs-lookup"><span data-stu-id="2fa93-829">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="2fa93-830">Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-830">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="2fa93-831">Powyższy przykład będzie zachowywać się tak samo jak poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="2fa93-831">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="2fa93-832">Trasy atrybutu można także łączyć z dziedziczeniem.</span><span class="sxs-lookup"><span data-stu-id="2fa93-832">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="2fa93-833">Jest to szczególnie zaawansowane w połączeniu z zastępowaniem tokenu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-833">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="2fa93-834">Zastępowanie tokenu dotyczy również nazw tras zdefiniowanych przez trasy atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-834">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="2fa93-835">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-835">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="2fa93-836">Aby dopasować ogranicznik zamiany tokenu literału `[` lub `]` , należy to zrobić, powtarzając znak ( `[[` lub `]]` ).</span><span class="sxs-lookup"><span data-stu-id="2fa93-836">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="2fa93-837">Używanie transformatora parametrów do dostosowywania zastępowania tokenu</span><span class="sxs-lookup"><span data-stu-id="2fa93-837">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="2fa93-838">Zastępowanie tokenu można dostosować za pomocą transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-838">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="2fa93-839">Transformator parametrów implementuje `IOutboundParameterTransformer` i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-839">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="2fa93-840">Na przykład niestandardowy `SlugifyParameterTransformer` transformator parametrów zmienia `SubscriptionManagement` wartość trasy na `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-840">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="2fa93-841">`RouteTokenTransformerConvention`Jest konwencją modelu aplikacji, która:</span><span class="sxs-lookup"><span data-stu-id="2fa93-841">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="2fa93-842">Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-842">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="2fa93-843">Dostosowuje wartości tokenów tras w miarę ich wymiany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-843">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="2fa93-844">`RouteTokenTransformerConvention`Jest zarejestrowany jako opcja w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-844">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="2fa93-845">Wiele tras</span><span class="sxs-lookup"><span data-stu-id="2fa93-845">Multiple Routes</span></span>

<span data-ttu-id="2fa93-846">Routing atrybutów obsługuje definiowanie wielu tras, które docierają do tej samej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-846">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="2fa93-847">Najczęstszym sposobem użycia tej metody jest naśladowanie zachowania *domyślnej trasy konwencjonalnej* , jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2fa93-847">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="2fa93-848">Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każda z nich będzie łączyć się z poszczególnymi atrybutami trasy w metodach akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-848">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="2fa93-849">Gdy wiele atrybutów trasy (które Implementuj `IActionConstraint` ) jest umieszczanych w akcji, każde ograniczenie akcji łączy się z szablonem trasy z atrybutu, który go zdefiniował.</span><span class="sxs-lookup"><span data-stu-id="2fa93-849">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="2fa93-850">Chociaż używanie wielu tras w akcjach może wydawać się zaawansowane, lepiej jest zachować prosty i dobrze zdefiniowany obszar adresów URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-850">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="2fa93-851">Użyj wielu tras w przypadku akcji tylko wtedy, gdy jest to konieczne, na przykład do obsługi istniejących klientów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-851">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="2fa93-852">Określanie opcjonalnych parametrów trasy, wartości domyślnych i ograniczeń</span><span class="sxs-lookup"><span data-stu-id="2fa93-852">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="2fa93-853">Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="2fa93-853">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="2fa93-854">Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-854">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="2fa93-855">Niestandardowe atrybuty trasy przy użyciu`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="2fa93-855">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="2fa93-856">Wszystkie atrybuty trasy podane w strukturze ( `[Route(...)]` , `[HttpGet(...)]` itp.) implementują `IRouteTemplateProvider` interfejs.</span><span class="sxs-lookup"><span data-stu-id="2fa93-856">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="2fa93-857">MVC wyszukuje atrybuty klas kontrolera i metod akcji, gdy aplikacja jest uruchamiana i używa tych, które implementują w `IRouteTemplateProvider` celu utworzenia początkowego zestawu tras.</span><span class="sxs-lookup"><span data-stu-id="2fa93-857">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="2fa93-858">Można zaimplementować, `IRouteTemplateProvider` Aby zdefiniować własne atrybuty trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-858">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="2fa93-859">Każdy `IRouteTemplateProvider` z nich umożliwia definiowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:</span><span class="sxs-lookup"><span data-stu-id="2fa93-859">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="2fa93-860">Atrybut z powyższego przykładu automatycznie ustawia wartość `Template` na `"api/[controller]"` gdy `[MyApiController]` jest stosowany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-860">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="2fa93-861">Dostosowywanie tras atrybutów przy użyciu modelu aplikacji</span><span class="sxs-lookup"><span data-stu-id="2fa93-861">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="2fa93-862">*Model aplikacji* jest modelem obiektów tworzonym podczas uruchamiania ze wszystkimi metadanymi używanymi przez MVC do kierowania i wykonywania akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-862">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="2fa93-863">*Model aplikacji* zawiera wszystkie dane zebrane z atrybutów trasy (do `IRouteTemplateProvider` ).</span><span class="sxs-lookup"><span data-stu-id="2fa93-863">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="2fa93-864">Można napisać *konwencje* , aby zmodyfikować model aplikacji w czasie uruchamiania, aby dostosować sposób zachowania routingu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-864">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="2fa93-865">W tej sekcji przedstawiono prosty przykład dostosowywania routingu przy użyciu modelu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-865">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="2fa93-866">Routing mieszany: Routing atrybutów a konwencjonalny Routing</span><span class="sxs-lookup"><span data-stu-id="2fa93-866">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="2fa93-867">Aplikacje MVC mogą łączyć użycie konwencjonalnego routingu i routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2fa93-867">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="2fa93-868">Typowym zastosowaniem są trasy konwencjonalne dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.</span><span class="sxs-lookup"><span data-stu-id="2fa93-868">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="2fa93-869">Akcje są albo podkierowane do Konwencji lub kierowane przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="2fa93-869">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="2fa93-870">Umieszczenie trasy na kontrolerze lub akcja powoduje, że atrybut jest kierowany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-870">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="2fa93-871">Akcje definiujące trasy atrybutów nie mogą być osiągane za pomocą konwencjonalnych tras i vice versa.</span><span class="sxs-lookup"><span data-stu-id="2fa93-871">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="2fa93-872">**Każdy** atrybut trasy na kontrolerze powoduje kierowanie wszystkich akcji w atrybucie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="2fa93-872">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="2fa93-873">Co odróżnia dwa typy systemów routingu, proces stosowany po adresie URL pasuje do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-873">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="2fa93-874">W przypadku routingu konwencjonalnego wartości trasy z dopasowania są używane do wybierania akcji i kontrolera z tabeli odnośników wszystkich konwencjonalnych akcji kierowanych.</span><span class="sxs-lookup"><span data-stu-id="2fa93-874">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="2fa93-875">W obszarze Routing atrybutów każdy szablon jest już skojarzony z akcją i nie jest wymagany dalsze wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-875">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="2fa93-876">Złożone segmenty</span><span class="sxs-lookup"><span data-stu-id="2fa93-876">Complex segments</span></span>

<span data-ttu-id="2fa93-877">Złożone segmenty (na przykład `[Route("/dog{token}cat")]` ) są przetwarzane przez dopasowanie literałów z prawej strony do lewej w sposób niezachłanney.</span><span class="sxs-lookup"><span data-stu-id="2fa93-877">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="2fa93-878">Sprawdź [kod źródłowy](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) opisu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-878">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="2fa93-879">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="2fa93-879">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="2fa93-880">Generowanie adresu URL</span><span class="sxs-lookup"><span data-stu-id="2fa93-880">URL Generation</span></span>

<span data-ttu-id="2fa93-881">Aplikacje MVC mogą używać funkcji generowania adresów URL routingu do generowania linków URL do akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-881">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="2fa93-882">Generowanie adresów URL eliminuje adresy URL zakodowana, co sprawia, że kod jest bardziej niezawodny i konserwowany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-882">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="2fa93-883">Ta sekcja koncentruje się na funkcjach generowania adresów URL dostarczonych przez MVC i obejmuje tylko podstawowe informacje na temat sposobu działania generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-883">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="2fa93-884">Aby uzyskać szczegółowy opis generowania adresów URL, zobacz temat [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-884">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="2fa93-885">`IUrlHelper`Interfejs jest podstawową częścią infrastruktury między MVC i routingiem na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-885">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="2fa93-886">Wystąpienie `IUrlHelper` dostępne za pomocą właściwości można znaleźć `Url` w obszarze Kontrolery, widoki i składniki widoku.</span><span class="sxs-lookup"><span data-stu-id="2fa93-886">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="2fa93-887">W tym przykładzie `IUrlHelper` interfejs jest używany przez `Controller.Url` Właściwość w celu WYGENEROWANIA adresu URL dla innej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-887">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="2fa93-888">Jeśli aplikacja używa domyślnej trasy konwencjonalnej, wartość `url` zmiennej będzie ciąg ścieżki URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-888">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="2fa93-889">Ta ścieżka URL jest tworzona przez Routing przez połączenie wartości trasy z bieżącego żądania (wartości otoczenia), z wartościami przekazanymi do `Url.Action` i podstawianie tych wartości do szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="2fa93-889">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="2fa93-890">Każdy parametr trasy w szablonie trasy ma swoją wartość zastępowaną przez pasujące nazwy wartościami i wartościami otoczenia.</span><span class="sxs-lookup"><span data-stu-id="2fa93-890">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="2fa93-891">Parametr trasy, który nie ma wartości, może korzystać z wartości domyślnej, jeśli ma taką wartość, lub być pominięty, jeśli jest opcjonalny (tak jak w przypadku `id` w przypadku w tym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="2fa93-891">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="2fa93-892">Generowanie adresu URL zakończy się niepowodzeniem, jeśli którykolwiek z wymaganych parametrów trasy nie ma odpowiadającej wartości.</span><span class="sxs-lookup"><span data-stu-id="2fa93-892">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="2fa93-893">Jeśli generowanie adresów URL kończy się niepowodzeniem dla trasy, kolejna trasa zostanie ponowiona do momentu przetworzenia wszystkich tras lub znalezienia dopasowania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-893">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="2fa93-894">W `Url.Action` powyższym przykładzie przyjęto założenie konwencjonalnego routingu, ale generowanie adresów URL działa podobnie jak w przypadku routingu atrybutów, chociaż koncepcje różnią się.</span><span class="sxs-lookup"><span data-stu-id="2fa93-894">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="2fa93-895">W przypadku routingu konwencjonalnego wartości trasy służą do rozszerzania szablonu, a wartości trasy dla `controller` i `action` zwykle pojawiają się w tym szablonie — działa to, ponieważ adresy URL dopasowane przez Routing są zgodne z *Konwencją*.</span><span class="sxs-lookup"><span data-stu-id="2fa93-895">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="2fa93-896">W obszarze Routing atrybutów wartości trasy dla `controller` i `action` nie mogą występować w szablonie — zamiast tego są używane do wyszukiwania szablonu do użycia.</span><span class="sxs-lookup"><span data-stu-id="2fa93-896">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="2fa93-897">W tym przykładzie zastosowano Routing atrybutów:</span><span class="sxs-lookup"><span data-stu-id="2fa93-897">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="2fa93-898">MVC kompiluje tabelę odnośników wszystkich akcji przypisanych do atrybutu i odpowiada `controller` wartości i, `action` Aby wybrać szablon trasy do użycia na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-898">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="2fa93-899">W powyższym przykładzie `custom/url/to/destination` jest generowany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-899">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="2fa93-900">Generowanie adresów URL według nazwy akcji</span><span class="sxs-lookup"><span data-stu-id="2fa93-900">Generating URLs by action name</span></span>

<span data-ttu-id="2fa93-901">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-901">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="2fa93-902">`Action`) i wszystkie powiązane przeciążenia są oparte na tym pomysłie, że chcesz określić, do czego łączysz, określając nazwę kontrolera i nazwę akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-902">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="2fa93-903">W przypadku używania `Url.Action` , bieżąca wartość trasy dla `controller` i `action` jest określona dla Ciebie — wartości `controller` i `action` są częścią obu *wartości otoczenia* **i** *wartości*.</span><span class="sxs-lookup"><span data-stu-id="2fa93-903">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="2fa93-904">Metoda `Url.Action` zawsze używa bieżących wartości `action` i `controller` i wygeneruje ścieżkę URL, która jest przesyłana do bieżącej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-904">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="2fa93-905">Funkcja routingu próbuje użyć wartości w otoczeniu wartości, aby podać informacje, które nie zostały wprowadzone podczas generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-905">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="2fa93-906">Przy użyciu trasy podobnej do `{a}/{b}/{c}/{d}` i otoczenia `{ a = Alice, b = Bob, c = Carol, d = David }` Routing ma wystarczającą ilość informacji do WYGENEROWANIA adresu URL bez żadnych dodatkowych wartości — ponieważ wszystkie parametry trasy mają wartość.</span><span class="sxs-lookup"><span data-stu-id="2fa93-906">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="2fa93-907">Po dodaniu wartości wartość zostanie `{ d = Donovan }` `{ d = David }` zignorowana, a wygenerowana Ścieżka adresu URL `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-907">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="2fa93-908">Ścieżki URL są hierarchiczne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-908">URL paths are hierarchical.</span></span> <span data-ttu-id="2fa93-909">W powyższym przykładzie, jeśli dodano wartość `{ c = Cheryl }` , obie wartości `{ c = Carol, d = David }` byłyby ignorowane.</span><span class="sxs-lookup"><span data-stu-id="2fa93-909">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="2fa93-910">W takim przypadku nie jest już dostępna wartość `d` i generowanie adresu URL zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="2fa93-910">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="2fa93-911">Należy określić pożądaną wartość `c` i `d` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-911">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="2fa93-912">Można oczekiwać, że ten problem zostanie osiągnięty przy użyciu trasy domyślnej ( `{controller}/{action}/{id?}` ) — ale w takiej sytuacji rzadko napotkasz to zachowanie, ponieważ `Url.Action` zawsze jawnie określimy `controller` `action` wartość i.</span><span class="sxs-lookup"><span data-stu-id="2fa93-912">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="2fa93-913">Dłuższe przeciążenia `Url.Action` również pobierają dodatkowy obiekt *wartości trasy* , aby zapewnić wartości parametrów trasy innych niż `controller` i `action` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-913">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="2fa93-914">Najczęściej zobaczysz, że będzie on używany z usługą `id` `Url.Action("Buy", "Products", new { id = 17 })` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-914">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="2fa93-915">Według Konwencji obiekt *wartości trasy* jest zwykle obiektem typu anonimowego, ale może również być `IDictionary<>` lub *zwykłym starym obiektem platformy .NET*.</span><span class="sxs-lookup"><span data-stu-id="2fa93-915">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="2fa93-916">Wszystkie dodatkowe wartości trasy, które nie pasują do parametrów trasy, są umieszczane w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-916">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="2fa93-917">Aby utworzyć bezwzględny adres URL, Użyj przeciążenia, które akceptuje `protocol` :`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="2fa93-917">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="2fa93-918">Generowanie adresów URL według trasy</span><span class="sxs-lookup"><span data-stu-id="2fa93-918">Generating URLs by route</span></span>

<span data-ttu-id="2fa93-919">Powyższy kod wygeneruje adres URL przez przekazanie go do kontrolera i nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-919">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="2fa93-920">`IUrlHelper`zapewnia także `Url.RouteUrl` rodzinę metod.</span><span class="sxs-lookup"><span data-stu-id="2fa93-920">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="2fa93-921">Te metody są podobne do `Url.Action` , ale nie kopiują bieżących wartości `action` i `controller` do wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-921">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="2fa93-922">Najbardziej typowym zastosowaniem jest określenie nazwy trasy do użycia określonej trasy do wygenerowania adresu URL, na ogół *bez* określania kontrolera lub nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-922">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="2fa93-923">Generowanie adresów URL w kodzie HTML</span><span class="sxs-lookup"><span data-stu-id="2fa93-923">Generating URLs in HTML</span></span>

<span data-ttu-id="2fa93-924">`IHtmlHelper`zapewnia `HtmlHelper` metody `Html.BeginForm` i `Html.ActionLink` wygenerowanie `<form>` `<a>` odpowiednio elementów i.</span><span class="sxs-lookup"><span data-stu-id="2fa93-924">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="2fa93-925">Metody te używają `Url.Action` metody do generowania adresu URL i akceptują podobne argumenty.</span><span class="sxs-lookup"><span data-stu-id="2fa93-925">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="2fa93-926">`Url.RouteUrl`Pomocników dla programu `HtmlHelper` mają `Html.BeginRouteForm` `Html.RouteLink` podobną funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="2fa93-926">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="2fa93-927">TagHelpers Generuj adresy URL za pomocą `form` TagHelper i `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="2fa93-927">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="2fa93-928">Oba te zastosowania `IUrlHelper` dla ich implementacji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-928">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="2fa93-929">Aby uzyskać więcej informacji, zobacz [Praca z formularzami](../views/working-with-forms.md) .</span><span class="sxs-lookup"><span data-stu-id="2fa93-929">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="2fa93-930">W widokach `IUrlHelper` można korzystać z `Url` właściwości dla dowolnej generacji adresów URL ad hoc, która nie jest objęta powyższym.</span><span class="sxs-lookup"><span data-stu-id="2fa93-930">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="2fa93-931">Generowanie adresów URL w wynikach akcji</span><span class="sxs-lookup"><span data-stu-id="2fa93-931">Generating URLS in Action Results</span></span>

<span data-ttu-id="2fa93-932">Powyższe przykłady przedstawiono przy użyciu `IUrlHelper` w kontrolerze, podczas gdy najbardziej typowym użyciem na kontrolerze jest wygenerowanie adresu URL jako części wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-932">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="2fa93-933">`ControllerBase` `Controller` Klasy bazowe i zapewniają wygodne metody dla wyników akcji, które odwołują się do innej akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-933">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="2fa93-934">Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2fa93-934">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="2fa93-935">Metody fabryki wyników akcji postępują zgodnie z podobnym wzorcem do metod w `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-935">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="2fa93-936">Specjalny przypadek dla dedykowanych tras konwencjonalnych</span><span class="sxs-lookup"><span data-stu-id="2fa93-936">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="2fa93-937">Funkcja routingu konwencjonalnego może używać specjalnego rodzaju definicji trasy o nazwie *dedykowanej, konwencjonalnej trasy*.</span><span class="sxs-lookup"><span data-stu-id="2fa93-937">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="2fa93-938">W poniższym przykładzie trasa o nazwie `blog` jest dedykowaną umowną trasą.</span><span class="sxs-lookup"><span data-stu-id="2fa93-938">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2fa93-939">Użycie tych definicji tras `Url.Action("Index", "Home")` spowoduje wygenerowanie ścieżki URL `/` z `default` trasą, ale dlaczego?</span><span class="sxs-lookup"><span data-stu-id="2fa93-939">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="2fa93-940">Możliwe jest odpuszczenie, że wartości trasy `{ controller = Home, action = Index }` byłyby wystarczające do wygenerowania adresu URL przy użyciu `blog` , a wynikiem będzie `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-940">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="2fa93-941">Dedykowane konwencjonalne trasy polegają na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiadającego parametru trasy, który uniemożliwia trasie "zbyt zachłanne" z generowaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-941">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="2fa93-942">W takim przypadku wartości domyślne to `{ controller = Blog, action = Article }` , a ani nie `controller` `action` pojawiają się jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-942">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="2fa93-943">Gdy Routing wykonuje generowanie adresów URL, podane wartości muszą być zgodne z wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="2fa93-943">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="2fa93-944">Generowanie adresu URL `blog` nie powiedzie się, ponieważ wartości `{ controller = Home, action = Index }` nie są zgodne `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-944">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="2fa93-945">Następnie usługa routingu wraca do wypróbowania `default` , która się powiedzie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-945">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="2fa93-946">Obszary</span><span class="sxs-lookup"><span data-stu-id="2fa93-946">Areas</span></span>

<span data-ttu-id="2fa93-947">[Obszary](areas.md) są funkcją MVC służącą do organizowania powiązanych funkcji w grupie jako oddzielnej przestrzeni nazw routingu (dla akcji kontrolera) i struktury folderów (dla widoków).</span><span class="sxs-lookup"><span data-stu-id="2fa93-947">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="2fa93-948">Użycie obszarów umożliwia aplikacji posiadanie wielu kontrolerów o tej samej nazwie, o ile mają one różne *obszary*.</span><span class="sxs-lookup"><span data-stu-id="2fa93-948">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="2fa93-949">Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie kolejnego parametru trasy `area` do `controller` i `action` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-949">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="2fa93-950">W tej sekcji omówiono sposób, w jaki Routing współdziała z obszarami — zobacz [obszary](areas.md) , aby uzyskać szczegółowe informacje o tym, jak obszary są używane w widokach.</span><span class="sxs-lookup"><span data-stu-id="2fa93-950">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="2fa93-951">Poniższy przykład konfiguruje MVC do używania domyślnej trasy konwencjonalnej i *trasy obszaru* dla obszaru o nazwie `Blog` :</span><span class="sxs-lookup"><span data-stu-id="2fa93-951">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="2fa93-952">W przypadku dopasowania ścieżki adresu URL `/Manage/Users/AddUser` , na przykład, Pierwsza trasa spowoduje wygenerowanie wartości trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-952">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="2fa93-953">`area`Wartość trasy jest generowana przez wartość domyślną dla `area` , w rzeczywistości trasa utworzona przez `MapAreaRoute` jest równoważna z następującymi:</span><span class="sxs-lookup"><span data-stu-id="2fa93-953">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="2fa93-954">`MapAreaRoute`tworzy trasę przy użyciu wartości domyślnej i ograniczenia dotyczącej `area` użycia podanej nazwy obszaru, w tym przypadku `Blog` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-954">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="2fa93-955">Wartość domyślna zapewnia, że trasa zawsze produkuje `{ area = Blog, ... }` , ograniczenie wymaga wartości `{ area = Blog, ... }` dla generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-955">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="2fa93-956">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="2fa93-956">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2fa93-957">Ogólnie rzecz biorąc, trasy z obszarami należy umieścić wcześniej w tabeli tras, ponieważ są one bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="2fa93-957">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2fa93-958">Korzystając z powyższego przykładu, wartości trasy będą zgodne z następującą akcją:</span><span class="sxs-lookup"><span data-stu-id="2fa93-958">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="2fa93-959">`AreaAttribute`Wskazuje to, co oznacza kontroler w ramach obszaru. Załóżmy, że ten kontroler znajduje się w `Blog` obszarze.</span><span class="sxs-lookup"><span data-stu-id="2fa93-959">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="2fa93-960">Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru i **nie** będą zgodne, gdy `area` wartość trasy zostanie podana przez Routing.</span><span class="sxs-lookup"><span data-stu-id="2fa93-960">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="2fa93-961">W poniższym przykładzie tylko pierwszy kontroler może być zgodny z wartościami trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-961">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="2fa93-962">Przestrzeń nazw każdego kontrolera jest pokazana w tym miejscu w celu zapewnienia kompletności — w przeciwnym razie kontrolery będą mieć konflikt nazw i generują błąd kompilatora.</span><span class="sxs-lookup"><span data-stu-id="2fa93-962">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="2fa93-963">Przestrzenie nazw klas nie mają wpływu na Routing MVC.</span><span class="sxs-lookup"><span data-stu-id="2fa93-963">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="2fa93-964">Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko wtedy, gdy ich nazwa obszaru jest podana przez `area` wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-964">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="2fa93-965">Trzeci kontroler nie jest członkiem żadnego obszaru i można go dopasować tylko wtedy, gdy nie ma żadnej wartości parametru `area` Routing.</span><span class="sxs-lookup"><span data-stu-id="2fa93-965">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="2fa93-966">W warunkach pasujących do *nie ma żadnej*wartości, brak `area` wartości jest taka sama jak wartość `area` null lub pusty ciąg.</span><span class="sxs-lookup"><span data-stu-id="2fa93-966">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="2fa93-967">Podczas wykonywania akcji wewnątrz obszaru wartość trasy dla `area` będzie dostępna jako *wartość otoczenia* dla routingu do użycia na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-967">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="2fa93-968">Oznacza to, że domyślnie obszary programu *Sticky Notes* mają być używane do generowania adresów URL, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-968">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="2fa93-969">Zrozumienie IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="2fa93-969">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="2fa93-970">Ta sekcja jest głęboką szczegółoweą wewnętrznych struktur oraz jak MVC wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="2fa93-970">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="2fa93-971">Typowa aplikacja nie będzie potrzebować niestandardowego`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="2fa93-971">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="2fa93-972">Prawdopodobnie już było używane, `IActionConstraint` nawet jeśli nie masz doświadczenia z interfejsem.</span><span class="sxs-lookup"><span data-stu-id="2fa93-972">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="2fa93-973">`[HttpGet]`Atrybut i podobne `[Http-VERB]` atrybuty implementują `IActionConstraint` w celu ograniczenia wykonywania metody akcji.</span><span class="sxs-lookup"><span data-stu-id="2fa93-973">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="2fa93-974">Przy założeniu domyślnej trasy konwencjonalnej, ścieżka URL `/Products/Edit` będzie generować wartości `{ controller = Products, action = Edit }` , które byłyby zgodne z **obiema** akcjami przedstawionymi w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-974">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="2fa93-975">W `IActionConstraint` terminologii mamy, że obie te akcje są uważane za kandydatów, ponieważ oba te są zgodne z danymi trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-975">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="2fa93-976">Gdy `HttpGetAttribute` zostanie wykonane, zobaczy, że *Edit ()* jest dopasowaniem do *Get* i nie jest dopasowaniem dla żadnego innego zlecenia http.</span><span class="sxs-lookup"><span data-stu-id="2fa93-976">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="2fa93-977">`Edit(...)`Akcja nie ma zdefiniowanych żadnych ograniczeń i dlatego będzie pasować do dowolnego czasownika http.</span><span class="sxs-lookup"><span data-stu-id="2fa93-977">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="2fa93-978">Dlatego przy założeniu, że `POST` `Edit(...)` dopasowań są tylko.</span><span class="sxs-lookup"><span data-stu-id="2fa93-978">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="2fa93-979">Jednak w przypadku `GET` obu akcji można nadal dopasować się, jednak akcja z funkcją `IActionConstraint` jest zawsze uznawana za *lepszą* od akcji bez.</span><span class="sxs-lookup"><span data-stu-id="2fa93-979">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="2fa93-980">Dlatego, że jest `Edit()` `[HttpGet]` traktowany jako bardziej szczegółowy i zostanie wybrany, jeśli obie akcje mogą być zgodne.</span><span class="sxs-lookup"><span data-stu-id="2fa93-980">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="2fa93-981">Koncepcyjnie, `IActionConstraint` jest formą *przeciążenia*, ale zamiast przeciążać metody o tej samej nazwie, przeciążanie między akcjami, które pasują do tego samego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2fa93-981">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="2fa93-982">Funkcja routingu atrybutów używa także `IActionConstraint` i może powodować, że działania z różnych kontrolerów są traktowane jako kandydaci.</span><span class="sxs-lookup"><span data-stu-id="2fa93-982">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="2fa93-983">Implementowanie IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="2fa93-983">Implementing IActionConstraint</span></span>

<span data-ttu-id="2fa93-984">Najprostszym sposobem implementacji `IActionConstraint` jest utworzenie klasy pochodnej od `System.Attribute` i umieszczenie jej w akcjach i kontrolerach.</span><span class="sxs-lookup"><span data-stu-id="2fa93-984">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="2fa93-985">MVC automatycznie odnajdzie wszystkie `IActionConstraint` , które są stosowane jako atrybuty.</span><span class="sxs-lookup"><span data-stu-id="2fa93-985">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="2fa93-986">Możesz użyć modelu aplikacji, aby zastosować ograniczenia i prawdopodobnie jest to najbardziej elastyczne podejście, ponieważ pozwala to na to, w jaki sposób są stosowane.</span><span class="sxs-lookup"><span data-stu-id="2fa93-986">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="2fa93-987">W poniższym przykładzie ograniczenie wybiera akcję na podstawie *kodu kraju* z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="2fa93-987">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="2fa93-988">[Pełny przykład w witrynie GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="2fa93-988">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="2fa93-989">Użytkownik jest odpowiedzialny za implementację `Accept` metody i wybranie "Order" dla ograniczenia, które ma zostać wykonane.</span><span class="sxs-lookup"><span data-stu-id="2fa93-989">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="2fa93-990">W tym przypadku `Accept` Metoda zwraca wartość, `true` aby zauważyć, że akcja jest dopasowanie, gdy `country` wartość trasy jest zgodna.</span><span class="sxs-lookup"><span data-stu-id="2fa93-990">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="2fa93-991">Różni się to od elementu `RouteValueAttribute` , który umożliwia powrót do akcji, która nie ma atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2fa93-991">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="2fa93-992">Przykład pokazuje, że w przypadku zdefiniowania `en-US` akcji, kod kraju, taki jak, powróci `fr-FR` do bardziej ogólnego kontrolera, który nie został `[CountrySpecific(...)]` zastosowany.</span><span class="sxs-lookup"><span data-stu-id="2fa93-992">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="2fa93-993">`Order`Właściwość decyduje, *który etap* jest częścią tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="2fa93-993">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="2fa93-994">Ograniczenia akcji są uruchamiane w grupach na podstawie `Order` .</span><span class="sxs-lookup"><span data-stu-id="2fa93-994">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="2fa93-995">Na przykład wszystkie atrybuty metody HTTP podane przez platformę używają tej samej `Order` wartości, aby były uruchamiane na tym samym etapie.</span><span class="sxs-lookup"><span data-stu-id="2fa93-995">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="2fa93-996">Możesz mieć tyle etapów, ile potrzebujesz, aby zaimplementować odpowiednie zasady.</span><span class="sxs-lookup"><span data-stu-id="2fa93-996">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="2fa93-997">Aby zdecydować, `Order` czy należy zastosować ograniczenie, przed metodami http.</span><span class="sxs-lookup"><span data-stu-id="2fa93-997">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="2fa93-998">Najpierw należy uruchomić mniejsze numery.</span><span class="sxs-lookup"><span data-stu-id="2fa93-998">Lower numbers run first.</span></span>

::: moniker-end
