---
<span data-ttu-id="15853-101">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-102">'Blazor'</span></span>
- <span data-ttu-id="15853-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-103">'Identity'</span></span>
- <span data-ttu-id="15853-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-105">'Razor'</span></span>
- <span data-ttu-id="15853-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="15853-107">Routing do akcji kontrolera w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="15853-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="15853-108">[Ryan Nowak](https://github.com/rynowak), [Kirka Larkin](https://twitter.com/serpent5)i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="15853-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="15853-109">Kontrolery ASP.NET Core używają [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) routingu do dopasowywania adresów URL żądań przychodzących i mapują je na [Akcje](#action).</span><span class="sxs-lookup"><span data-stu-id="15853-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="15853-110">Szablony tras:</span><span class="sxs-lookup"><span data-stu-id="15853-110">Routes templates:</span></span>

* <span data-ttu-id="15853-111">Są zdefiniowane w kodzie startowym lub atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="15853-112">Opisz, w jaki sposób ścieżki URL są dopasowywane do [akcji](#action).</span><span class="sxs-lookup"><span data-stu-id="15853-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="15853-113">Służy do generowania adresów URL dla łączy.</span><span class="sxs-lookup"><span data-stu-id="15853-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="15853-114">Wygenerowane linki są zwykle zwracane w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="15853-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="15853-115">Akcje są albo [podkierowane do Konwencji](#cr) lub [kierowane przez atrybut](#ar).</span><span class="sxs-lookup"><span data-stu-id="15853-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="15853-116">Umieszczenie trasy na kontrolerze lub [akcji](#action) sprawia, że jest ona kierowana przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="15853-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="15853-117">Aby uzyskać więcej informacji, zobacz [Routing mieszany](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="15853-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="15853-118">Ten dokument:</span><span class="sxs-lookup"><span data-stu-id="15853-118">This document:</span></span>

* <span data-ttu-id="15853-119">Wyjaśnia interakcje między MVC i Routing:</span><span class="sxs-lookup"><span data-stu-id="15853-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="15853-120">Jak typowe aplikacje MVC używają funkcji routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="15853-121">Obejmuje zarówno:</span><span class="sxs-lookup"><span data-stu-id="15853-121">Covers both:</span></span>
    * <span data-ttu-id="15853-122">[UNC routingu](#cr) zwykle używany z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="15853-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="15853-123">*Routing atrybutów* używany z interfejsami API REST.</span><span class="sxs-lookup"><span data-stu-id="15853-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="15853-124">Jeśli jesteś głównie zainteresowani routingiem interfejsów API REST, przejdź do sekcji [Routing atrybutów dla interfejsów API REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="15853-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="15853-125">Szczegóły dotyczące routingu zaawansowanego znajdują się w temacie [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="15853-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="15853-126">Odnosi się do domyślnego systemu routingu dodanego w ASP.NET Core 3,0, nazywanego routingiem punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="15853-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="15853-127">Możliwe jest używanie kontrolerów z poprzednią wersją routingu w celu zapewnienia zgodności.</span><span class="sxs-lookup"><span data-stu-id="15853-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="15853-128">Instrukcje można znaleźć w [przewodniku migracji 2.2-3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="15853-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="15853-129">Zapoznaj się z [wersją 2,2 tego dokumentu](xref:mvc/controllers/routing?view=aspnetcore-2.2) dotyczącą materiałów referencyjnych w starszym systemie routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="15853-130">Konfigurowanie trasy konwencjonalnej</span><span class="sxs-lookup"><span data-stu-id="15853-130">Set up conventional route</span></span>

<span data-ttu-id="15853-131">`Startup.Configure`w przypadku korzystania z [konwencjonalnego routingu](#crd)zazwyczaj ma kod podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="15853-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="15853-132">Wewnątrz wywołania do <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> , służy <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> do tworzenia pojedynczej trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="15853-133">Pojedyncza trasa ma nazwę `default` Route.</span><span class="sxs-lookup"><span data-stu-id="15853-133">The single route is named `default` route.</span></span> <span data-ttu-id="15853-134">Większość aplikacji z kontrolerami i widokami używa szablonu trasy podobnego do `default` trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="15853-135">Interfejsy API REST powinny używać [routingu atrybutów](#ar).</span><span class="sxs-lookup"><span data-stu-id="15853-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="15853-136">Szablon trasy `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="15853-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="15853-137">Dopasowuje ścieżkę URL, taką jak`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="15853-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="15853-138">Wyodrębnia wartości trasy `{ controller = Products, action = Details, id = 5 }` przez tokenizowanie ścieżki.</span><span class="sxs-lookup"><span data-stu-id="15853-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="15853-139">Wyodrębnienie wartości tras powoduje dopasowanie, jeśli aplikacja ma kontroler o nazwie `ProductsController` i `Details` akcję:</span><span class="sxs-lookup"><span data-stu-id="15853-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="15853-140">`/Products/Details/5`Model wiąże wartość, `id = 5` Aby ustawić parametr jako `id` `5` .</span><span class="sxs-lookup"><span data-stu-id="15853-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="15853-141">Aby uzyskać więcej informacji, zobacz [powiązanie modelu](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="15853-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="15853-142">`{controller=Home}`definiuje `Home` jako domyślny `controller` .</span><span class="sxs-lookup"><span data-stu-id="15853-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="15853-143">`{action=Index}`definiuje `Index` jako domyślny `action` .</span><span class="sxs-lookup"><span data-stu-id="15853-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="15853-144">`?`Znak w `{id?}` definiuje `id` jako opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="15853-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="15853-145">Domyślne i opcjonalne parametry trasy nie muszą być obecne w ścieżce URL dla dopasowania.</span><span class="sxs-lookup"><span data-stu-id="15853-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="15853-146">Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="15853-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="15853-147">Dopasowuje ścieżkę URL `/` .</span><span class="sxs-lookup"><span data-stu-id="15853-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="15853-148">Tworzy wartości trasy `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="15853-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="15853-149">Wartości dla `controller` i `action` używają wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="15853-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="15853-150">`id`nie produkuje wartości, ponieważ nie ma odpowiedniego segmentu w ścieżce adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="15853-151">`/`dopasowuje się tylko wtedy, gdy istnieje `HomeController` `Index` Akcja i:</span><span class="sxs-lookup"><span data-stu-id="15853-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="15853-152">Przy użyciu definicji kontrolera i szablonu trasy, `HomeController.Index` Akcja jest uruchamiana dla następujących ścieżek URL:</span><span class="sxs-lookup"><span data-stu-id="15853-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="15853-153">Ścieżka adresu URL `/` używa domyślnych `Home` kontrolerów i akcji szablonu trasy `Index` .</span><span class="sxs-lookup"><span data-stu-id="15853-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="15853-154">Ścieżka adresu URL `/Home` używa domyślnej akcji szablonu trasy `Index` .</span><span class="sxs-lookup"><span data-stu-id="15853-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="15853-155">Wygodna metoda <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> :</span><span class="sxs-lookup"><span data-stu-id="15853-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="15853-156">Zastępując</span><span class="sxs-lookup"><span data-stu-id="15853-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="15853-157">Routing jest konfigurowany przy użyciu programu <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> i <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="15853-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="15853-158">Aby użyć kontrolerów:</span><span class="sxs-lookup"><span data-stu-id="15853-158">To use controllers:</span></span>
>
> * <span data-ttu-id="15853-159">Wywołaj <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> wewnątrz `UseEndpoints` , aby zmapować kontrolery z [routingiem atrybutów](#ar) .</span><span class="sxs-lookup"><span data-stu-id="15853-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="15853-160">Wywołaj <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> lub <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , aby zamapować kontrolery z [Konwencją routingu](#cr) .</span><span class="sxs-lookup"><span data-stu-id="15853-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="15853-161">Routing konwencjonalny</span><span class="sxs-lookup"><span data-stu-id="15853-161">Conventional routing</span></span>

<span data-ttu-id="15853-162">Routing konwencjonalny jest używany z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="15853-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="15853-163">`default`Trasa:</span><span class="sxs-lookup"><span data-stu-id="15853-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="15853-164">jest przykładem *konwencjonalnego routingu*.</span><span class="sxs-lookup"><span data-stu-id="15853-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="15853-165">Jest on nazywany *konwencjonalnym routingiem* , ponieważ ustanawia *Konwencję* dla ścieżek adresów URL:</span><span class="sxs-lookup"><span data-stu-id="15853-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="15853-166">Pierwszy segment ścieżki, `{controller=Home}` mapuje na nazwę kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="15853-167">Drugi segment, `{action=Index}` , mapuje na nazwę [akcji](#action) .</span><span class="sxs-lookup"><span data-stu-id="15853-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="15853-168">Trzeci segment `{id?}` jest używany jako opcjonalny `id` .</span><span class="sxs-lookup"><span data-stu-id="15853-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="15853-169">`?`W programie w programie `{id?}` jest to opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="15853-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="15853-170">`id`służy do mapowania na jednostkę modelu.</span><span class="sxs-lookup"><span data-stu-id="15853-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="15853-171">Przy użyciu tej `default` trasy ścieżka URL:</span><span class="sxs-lookup"><span data-stu-id="15853-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="15853-172">`/Products/List`mapuje do `ProductsController.List` akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="15853-173">`/Blog/Article/17`mapowanie na `BlogController.Article` model i zwykle wiąże się z `id` parametrem 17.</span><span class="sxs-lookup"><span data-stu-id="15853-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="15853-174">To mapowanie:</span><span class="sxs-lookup"><span data-stu-id="15853-174">This mapping:</span></span>

* <span data-ttu-id="15853-175">Jest oparty **tylko**na nazwach kontrolera i [akcji](#action) .</span><span class="sxs-lookup"><span data-stu-id="15853-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="15853-176">Nie jest oparty na przestrzeniach nazw, lokalizacjach plików źródłowych ani parametrach metod.</span><span class="sxs-lookup"><span data-stu-id="15853-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="15853-177">Użycie konwencjonalnego routingu z domyślną trasą umożliwia tworzenie aplikacji bez konieczności korzystania z nowego wzorca adresu URL dla każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="15853-178">W przypadku aplikacji z akcjami w stylu [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) mających spójność dla adresów URL między kontrolerami:</span><span class="sxs-lookup"><span data-stu-id="15853-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="15853-179">Upraszcza kod.</span><span class="sxs-lookup"><span data-stu-id="15853-179">Helps simplify the code.</span></span>
* <span data-ttu-id="15853-180">Sprawia, że interfejs użytkownika jest bardziej przewidywalny.</span><span class="sxs-lookup"><span data-stu-id="15853-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="15853-181">`id`W powyższym kodzie jest zdefiniowany jako opcjonalny przez szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="15853-182">Akcje można wykonać bez opcjonalnego identyfikatora podanego w ramach adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="15853-183">Ogólnie mówiąc, gdy `id` pominięto w adresie URL:</span><span class="sxs-lookup"><span data-stu-id="15853-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="15853-184">`id`jest ustawiony na `0` przez powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="15853-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="15853-185">Nie znaleziono jednostki w dopasowaniu do bazy danych `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="15853-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="15853-186">[Routing atrybutu](#ar) zawiera szczegółowy formant, który umożliwia określanie identyfikatora wymaganego dla niektórych akcji, a nie dla innych.</span><span class="sxs-lookup"><span data-stu-id="15853-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="15853-187">Zgodnie z Konwencją, dokumentacja zawiera opcjonalne parametry, takie jak, `id` gdy prawdopodobnie pojawi się w prawidłowym użyciu.</span><span class="sxs-lookup"><span data-stu-id="15853-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="15853-188">Większość aplikacji powinna wybrać podstawowy i opisowy schemat routingu, aby adresy URL były czytelne i zrozumiałe.</span><span class="sxs-lookup"><span data-stu-id="15853-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="15853-189">Domyślna trasa konwencjonalna `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="15853-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="15853-190">Obsługuje podstawowy i opisowy schemat routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="15853-191">Jest użytecznym punktem wyjścia dla aplikacji opartych na interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="15853-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="15853-192">Jest jedynym szablonem trasy wymaganym przez wiele aplikacji interfejsu użytkownika sieci Web.</span><span class="sxs-lookup"><span data-stu-id="15853-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="15853-193">W przypadku większych aplikacji interfejsu użytkownika sieci Web inna trasa korzysta z [obszarów](#areas) , jeśli są one często używane.</span><span class="sxs-lookup"><span data-stu-id="15853-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="15853-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>i <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="15853-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="15853-195">Automatycznie Przypisz wartość **zamówienia** do punktów końcowych na podstawie kolejności, w której są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="15853-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="15853-196">Routing punktów końcowych w ASP.NET Core 3,0 i nowszych:</span><span class="sxs-lookup"><span data-stu-id="15853-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="15853-197">Nie ma koncepcji tras.</span><span class="sxs-lookup"><span data-stu-id="15853-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="15853-198">Nie oferuje gwarancji porządkowania dla wykonywania rozszerzalności, wszystkie punkty końcowe są przetwarzane jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="15853-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="15853-199">Włącz [Rejestrowanie](xref:fundamentals/logging/index) , aby zobaczyć, jak wbudowane implementacje routingu, takie jak <xref:Microsoft.AspNetCore.Routing.Route> , dopasowują żądania.</span><span class="sxs-lookup"><span data-stu-id="15853-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="15853-200">[Routing atrybutów](#ar) został wyjaśniony w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="15853-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="15853-201">Wiele konwencjonalnych tras</span><span class="sxs-lookup"><span data-stu-id="15853-201">Multiple conventional routes</span></span>

<span data-ttu-id="15853-202">Wewnątrz można dodać wiele [konwencjonalnych tras](#cr) `UseEndpoints` , dodając więcej wywołań do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> i <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="15853-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="15853-203">Dzięki temu można definiować wiele Konwencji lub dodać do nich trasy konwencjonalne, które są przeznaczone dla konkretnej [akcji](#action), na przykład:</span><span class="sxs-lookup"><span data-stu-id="15853-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="15853-204">`blog`Trasa w powyższym kodzie jest **dedykowaną konwencjonalną trasą**.</span><span class="sxs-lookup"><span data-stu-id="15853-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="15853-205">Jest on nazywany dedykowaną trasą konwencjonalną, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="15853-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="15853-206">Używa ona [konwencjonalnego routingu](#cr).</span><span class="sxs-lookup"><span data-stu-id="15853-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="15853-207">Jest on przeznaczony dla konkretnej [akcji](#action).</span><span class="sxs-lookup"><span data-stu-id="15853-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="15853-208">Ponieważ `controller` i `action` nie pojawiają się w szablonie trasy `"blog/{*article}"` jako parametry:</span><span class="sxs-lookup"><span data-stu-id="15853-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="15853-209">Mogą mieć tylko wartości domyślne `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="15853-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="15853-210">Ta trasa zawsze jest mapowana na akcję `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="15853-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="15853-211">`/Blog`, `/Blog/Article` i `/Blog/{any-string}` są jedynymi ŚCIEŻKAmi URL, które pasują do trasy blogu.</span><span class="sxs-lookup"><span data-stu-id="15853-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="15853-212">Powyższy przykład:</span><span class="sxs-lookup"><span data-stu-id="15853-212">The preceding example:</span></span>

* <span data-ttu-id="15853-213">`blog`trasa ma wyższy priorytet dla dopasowania niż trasa, `default` ponieważ jest dodawana jako pierwsza.</span><span class="sxs-lookup"><span data-stu-id="15853-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="15853-214">Jest i przykładem routingu stylów [informacji](https://developer.mozilla.org/docs/Glossary/Slug) o sobie, w którym typowym jest nazwa artykułu w ramach adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="15853-215">W ASP.NET Core 3,0 i nowszych routingu nie są:</span><span class="sxs-lookup"><span data-stu-id="15853-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="15853-216">Zdefiniuj koncepcję o nazwie *trasa*.</span><span class="sxs-lookup"><span data-stu-id="15853-216">Define a concept called a *route*.</span></span> <span data-ttu-id="15853-217">`UseRouting`dodaje dopasowanie trasy do potoku programu pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="15853-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="15853-218">`UseRouting`Oprogramowanie pośredniczące sprawdza zestaw punktów końcowych zdefiniowanych w aplikacji i wybiera najlepsze dopasowanie punktu końcowego na podstawie żądania.</span><span class="sxs-lookup"><span data-stu-id="15853-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="15853-219">Podaj gwarancje dotyczące kolejności wykonywania rozszerzalności, takich jak <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> lub <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="15853-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="15853-220">Zobacz [Routing](xref:fundamentals/routing) dla materiałów referencyjnych na trasie.</span><span class="sxs-lookup"><span data-stu-id="15853-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="15853-221">Tradycyjna kolejność routingu</span><span class="sxs-lookup"><span data-stu-id="15853-221">Conventional routing order</span></span>

<span data-ttu-id="15853-222">Routing konwencjonalny pasuje do kombinacji akcji i kontrolera, które są zdefiniowane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="15853-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="15853-223">Jest to przeznaczone do uproszczenia sytuacji, w których trasy konwencjonalne nakładają się na siebie.</span><span class="sxs-lookup"><span data-stu-id="15853-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="15853-224">Dodanie tras przy użyciu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> i <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatycznie przypisanie wartości zamówienia do punktów końcowych w oparciu o kolejność, w której są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="15853-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="15853-225">Dopasowania z trasy, która pojawiła się wcześniej, mają wyższy priorytet.</span><span class="sxs-lookup"><span data-stu-id="15853-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="15853-226">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="15853-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="15853-227">Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczone wcześniej, ponieważ są bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="15853-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="15853-228">[Dedykowane konwencjonalne trasy](#dcr) ze wszystkimi parametrami tras, takich jak, `{*article}` mogą spowodować zbyt [zachłanne](xref:fundamentals/routing#greedy)trasy, co oznacza, że pasuje do adresów URL, które mają być dopasowane przez inne trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="15853-229">Umieść trasy zachłanne w dalszej części tabeli tras, aby zapobiec dopasowania zachłanne.</span><span class="sxs-lookup"><span data-stu-id="15853-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="15853-230">Rozwiązywanie niejednoznacznych akcji</span><span class="sxs-lookup"><span data-stu-id="15853-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="15853-231">Gdy dwa punkty końcowe pasują do routingu, routing musi wykonać jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="15853-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="15853-232">Wybierz najlepszego kandydata.</span><span class="sxs-lookup"><span data-stu-id="15853-232">Choose the best candidate.</span></span>
* <span data-ttu-id="15853-233">Zgłoś wyjątek.</span><span class="sxs-lookup"><span data-stu-id="15853-233">Throw an exception.</span></span>

<span data-ttu-id="15853-234">Przykład:</span><span class="sxs-lookup"><span data-stu-id="15853-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="15853-235">Poprzedni kontroler definiuje dwie akcje, które są zgodne:</span><span class="sxs-lookup"><span data-stu-id="15853-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="15853-236">Ścieżka adresu URL`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="15853-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="15853-237">Kierowanie danych `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="15853-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="15853-238">Jest to typowy wzorzec dla kontrolerów MVC:</span><span class="sxs-lookup"><span data-stu-id="15853-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="15853-239">`Edit(int)`Wyświetla formularz, w którym można edytować produkt.</span><span class="sxs-lookup"><span data-stu-id="15853-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="15853-240">`Edit(int, Product)`przetwarza opublikowany formularz.</span><span class="sxs-lookup"><span data-stu-id="15853-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="15853-241">Aby rozwiązać poprawność trasy:</span><span class="sxs-lookup"><span data-stu-id="15853-241">To resolve the correct route:</span></span>

* <span data-ttu-id="15853-242">`Edit(int, Product)`jest wybierany, gdy żądanie jest HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="15853-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="15853-243">`Edit(int)`jest wybierany, gdy [czasownik http](#verb) jest inny.</span><span class="sxs-lookup"><span data-stu-id="15853-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="15853-244">`Edit(int)`jest zazwyczaj wywoływany za pośrednictwem `GET` .</span><span class="sxs-lookup"><span data-stu-id="15853-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="15853-245"><xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]` ,, Jest dostarczany do routingu, aby można było wybrać oparty na metodzie HTTP żądania.</span><span class="sxs-lookup"><span data-stu-id="15853-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="15853-246">`HttpPostAttribute`Zapewnia `Edit(int, Product)` lepszy odpowiednik niż `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="15853-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="15853-247">Ważne jest, aby zrozumieć rolę atrybutów, takich jak `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="15853-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="15853-248">Podobne atrybuty są zdefiniowane dla innych [czasowników HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="15853-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="15853-249">W przypadku [routingu konwencjonalnego](#cr)typowe dla akcji używanie tej samej nazwy akcji, gdy są one częścią formularza Pokaż, przesyłania formularza.</span><span class="sxs-lookup"><span data-stu-id="15853-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="15853-250">Na przykład zapoznaj [się z tematem badanie dwóch metod akcji edycji](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="15853-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="15853-251">Jeśli routingu nie można wybrać najlepszego kandydata, <xref:System.Reflection.AmbiguousMatchException> zostanie zgłoszony, zawierający wiele pasujących punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="15853-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="15853-252">Nazwy tras konwencjonalnych</span><span class="sxs-lookup"><span data-stu-id="15853-252">Conventional route names</span></span>

<span data-ttu-id="15853-253">Ciągi `"blog"` i `"default"` w poniższych przykładach są nazwami konwencjonalnych tras:</span><span class="sxs-lookup"><span data-stu-id="15853-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="15853-254">Nazwy tras nadają trasie nazwę logiczną.</span><span class="sxs-lookup"><span data-stu-id="15853-254">The route names give the route a logical name.</span></span> <span data-ttu-id="15853-255">Nazwana trasa może służyć do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="15853-256">Użycie nazwanej trasy upraszcza tworzenie adresów URL, gdy porządkowanie tras może spowodować skomplikowane generowanie adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="15853-257">Nazwy tras muszą być unikatowe w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="15853-258">Nazwy tras:</span><span class="sxs-lookup"><span data-stu-id="15853-258">Route names:</span></span>

* <span data-ttu-id="15853-259">Nie mają wpływu na pasujące adresy URL ani obsługę żądań.</span><span class="sxs-lookup"><span data-stu-id="15853-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="15853-260">Są używane tylko do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-260">Are used only for URL generation.</span></span>

<span data-ttu-id="15853-261">Koncepcja nazwy trasy jest reprezentowana w obszarze Routing jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="15853-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="15853-262">**Nazwa trasy** i nazwa **punktu końcowego**:</span><span class="sxs-lookup"><span data-stu-id="15853-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="15853-263">Są zamienne.</span><span class="sxs-lookup"><span data-stu-id="15853-263">Are interchangeable.</span></span>
* <span data-ttu-id="15853-264">Który jest używany w dokumentacji i kodu zależy od opisanego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="15853-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="15853-265">Routing atrybutów dla interfejsów API REST</span><span class="sxs-lookup"><span data-stu-id="15853-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="15853-266">Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcjonalności aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez [zlecenia http](#verb).</span><span class="sxs-lookup"><span data-stu-id="15853-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="15853-267">Funkcja routingu atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="15853-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="15853-268">Poniższy `StartUp.Configure` kod jest typowy dla interfejsu API REST i jest używany w następnym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="15853-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="15853-269">W powyższym kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> jest wywoływana w `UseEndpoints` celu mapowania kontrolerów z routingiem atrybutu.</span><span class="sxs-lookup"><span data-stu-id="15853-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="15853-270">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="15853-270">In the following example:</span></span>

* <span data-ttu-id="15853-271">Poprzednia `Configure` Metoda jest używana.</span><span class="sxs-lookup"><span data-stu-id="15853-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="15853-272">`HomeController`dopasowuje zestaw adresów URL podobny do tego, co jest zgodne z domyślną trasą konwencjonalny `{controller=Home}/{action=Index}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="15853-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="15853-273">`HomeController.Index`Akcja jest uruchamiana dla dowolnej ścieżki URL,, `/` `/Home` `/Home/Index` , lub `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="15853-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="15853-274">W tym przykładzie przedstawiono najważniejsze różnice programistyczne między routingiem atrybutów i [routingiem konwencjonalnym](#cr).</span><span class="sxs-lookup"><span data-stu-id="15853-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="15853-275">Routing atrybutów wymaga więcej danych wejściowych w celu określenia trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="15853-276">Konwencjonalne trasy domyślne obsługuje trasy bardziej zwięzłie.</span><span class="sxs-lookup"><span data-stu-id="15853-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="15853-277">Jednak Routing atrybutu zezwala na ścisłą kontrolę nad tym, które szablony tras mają zastosowanie do poszczególnych [akcji](#action).</span><span class="sxs-lookup"><span data-stu-id="15853-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="15853-278">W poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="15853-278">In the following code:</span></span>

* <span data-ttu-id="15853-279">Nazwa kontrolera i nazwy akcji nie odgrywają **żadnej** roli, w której akcja jest dopasowana.</span><span class="sxs-lookup"><span data-stu-id="15853-279">The controller name and action names play **no** role in which action is matched.</span></span>
* <span data-ttu-id="15853-280">Dopasowuje te same adresy URL, co w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="15853-280">Matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="15853-281">Następujący kod używa zastąpienia tokenu dla `action` i `controller` :</span><span class="sxs-lookup"><span data-stu-id="15853-281">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="15853-282">Następujący kod ma zastosowanie `[Route("[controller]/[action]")]` do kontrolera:</span><span class="sxs-lookup"><span data-stu-id="15853-282">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="15853-283">W poprzednim kodzie, `Index` Szablony metod muszą być poprzedzone `/` lub `~/` do szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="15853-283">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="15853-284">Szablony tras zastosowane do akcji rozpoczynającej się od `/` lub `~/` nie są połączone z szablonami tras zastosowanymi do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-284">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="15853-285">Zobacz [pierwszeństwo szablonu trasy](xref:fundamentals/routing#rtp) , aby uzyskać informacje o wyborze szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-285">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="15853-286">Zastrzeżone nazwy routingu</span><span class="sxs-lookup"><span data-stu-id="15853-286">Reserved routing names</span></span>

<span data-ttu-id="15853-287">Następujące słowa kluczowe są zastrzeżonymi nazwami parametrów trasy podczas korzystania z kontrolerów lub Razor stron:</span><span class="sxs-lookup"><span data-stu-id="15853-287">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="15853-288">Użycie `page` jako parametru trasy z routingiem atrybutu jest typowym błędem.</span><span class="sxs-lookup"><span data-stu-id="15853-288">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="15853-289">Powoduje to niespójne i mylące zachowanie przy generowaniu adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-289">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="15853-290">Specjalne nazwy parametrów są używane przez generowanie adresów URL w celu ustalenia, czy operacja generowania adresu URL odwołuje się do Razor strony lub do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-290">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="15853-291">Szablony czasowników HTTP</span><span class="sxs-lookup"><span data-stu-id="15853-291">HTTP verb templates</span></span>

<span data-ttu-id="15853-292">ASP.NET Core ma następujące szablony czasowników HTTP:</span><span class="sxs-lookup"><span data-stu-id="15853-292">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="15853-293">[Narzędzia HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="15853-293">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="15853-294">[HTTPPOST](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="15853-294">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="15853-295">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="15853-295">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="15853-296">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="15853-296">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="15853-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="15853-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="15853-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="15853-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="15853-299">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="15853-299">Route templates</span></span>

<span data-ttu-id="15853-300">ASP.NET Core ma następujące szablony tras:</span><span class="sxs-lookup"><span data-stu-id="15853-300">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="15853-301">Wszystkie [Szablony czasowników HTTP](#verb) są szablonami tras.</span><span class="sxs-lookup"><span data-stu-id="15853-301">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="15853-302">[Szlak](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="15853-302">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="15853-303">Routing atrybutów z atrybutami czasownik http</span><span class="sxs-lookup"><span data-stu-id="15853-303">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="15853-304">Rozważmy następujący kontroler:</span><span class="sxs-lookup"><span data-stu-id="15853-304">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="15853-305">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="15853-305">In the preceding code:</span></span>

* <span data-ttu-id="15853-306">Każda akcja zawiera `[HttpGet]` atrybut, który ogranicza dopasowywanie do żądań HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="15853-306">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="15853-307">`GetProduct`Akcja obejmuje `"{id}"` szablon, dlatego `id` jest dołączany do `"api/[controller]"` szablonu na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="15853-307">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="15853-308">Szablon metod to `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="15853-308">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="15853-309">W związku z tym ta akcja dopasowuje tylko żądania GET dla formularza `/api/test2/xyz` ,, `/api/test2/123` `/api/test2/{any string}` itp.</span><span class="sxs-lookup"><span data-stu-id="15853-309">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="15853-310">`GetIntProduct`Akcja zawiera `"int/{id:int}")` szablon.</span><span class="sxs-lookup"><span data-stu-id="15853-310">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="15853-311">`:int`Część szablonu ogranicza `id` wartości trasy do ciągów, które mogą być konwertowane na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="15853-311">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="15853-312">Żądanie GET `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="15853-312">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="15853-313">Nie jest zgodna z tą akcją.</span><span class="sxs-lookup"><span data-stu-id="15853-313">Doesn't match this action.</span></span>
  * <span data-ttu-id="15853-314">Zwraca błąd [nie znaleziono 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="15853-314">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="15853-315">`GetInt2Product`Akcja zawiera `{id}` w szablonie, ale nie ogranicza `id` wartości, które można przekonwertować na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="15853-315">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="15853-316">Żądanie GET `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="15853-316">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="15853-317">Pasuje do tej trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-317">Matches this route.</span></span>
  * <span data-ttu-id="15853-318">Nie można przekonwertować powiązania modelu `abc` na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="15853-318">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="15853-319">`id`Parametr metody jest liczbą całkowitą.</span><span class="sxs-lookup"><span data-stu-id="15853-319">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="15853-320">Zwraca [400 Nieprawidłowe żądanie](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , ponieważ nie można skonwertować powiązania modelu `abc` na liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="15853-320">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="15853-321">Funkcja routingu atrybutów może używać <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> atrybutów takich jak <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> , i <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="15853-321">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="15853-322">Wszystkie atrybuty [czasowników HTTP](#verb) akceptują szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-322">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="15853-323">Poniższy przykład przedstawia dwie akcje, które pasują do tego samego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="15853-323">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="15853-324">Przy użyciu ścieżki URL `/products3` :</span><span class="sxs-lookup"><span data-stu-id="15853-324">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="15853-325">`MyProductsController.ListProducts`Akcja jest uruchamiana, gdy [czasownik http](#verb) to `GET` .</span><span class="sxs-lookup"><span data-stu-id="15853-325">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="15853-326">`MyProductsController.CreateProduct`Akcja jest uruchamiana, gdy [czasownik http](#verb) to `POST` .</span><span class="sxs-lookup"><span data-stu-id="15853-326">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="15853-327">Podczas kompilowania interfejsu API REST jest to rzadko konieczne, aby użyć `[Route(...)]` metody akcji, ponieważ akcja akceptuje wszystkie metody http.</span><span class="sxs-lookup"><span data-stu-id="15853-327">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="15853-328">Lepiej jest używać bardziej szczegółowego [atrybutu zlecenia http](#verb) , aby precyzyjnie dowiedzieć się, co obsługuje interfejs API.</span><span class="sxs-lookup"><span data-stu-id="15853-328">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="15853-329">Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i czasowniki HTTP mapują na określone operacje logiczne.</span><span class="sxs-lookup"><span data-stu-id="15853-329">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="15853-330">Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcjonalności aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez zlecenia HTTP.</span><span class="sxs-lookup"><span data-stu-id="15853-330">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="15853-331">Oznacza to, że wiele operacji, na przykład Pobierz i Opublikuj dla tego samego zasobu logicznego, użyj tego samego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-331">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="15853-332">Routing atrybutu zapewnia poziom kontroli, który jest wymagany do dokładnego projektowania układu publicznego punktu końcowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="15853-332">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="15853-333">Ponieważ atrybut Route ma zastosowanie do określonej akcji, można łatwo wprowadzić parametry wymagane jako część definicji szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-333">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="15853-334">W poniższym przykładzie `id` jest wymagane jako część ścieżki URL:</span><span class="sxs-lookup"><span data-stu-id="15853-334">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="15853-335">`Products2ApiController.GetProduct(int)`Akcja:</span><span class="sxs-lookup"><span data-stu-id="15853-335">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="15853-336">Jest uruchamiany z ścieżką URL podobną do`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="15853-336">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="15853-337">Nie jest uruchamiany z ścieżką URL `/products2` .</span><span class="sxs-lookup"><span data-stu-id="15853-337">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="15853-338">Atrybut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) Requests] umożliwia akcja ograniczenia obsługiwanych typów zawartości żądania.</span><span class="sxs-lookup"><span data-stu-id="15853-338">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="15853-339">Aby uzyskać więcej informacji, zobacz [Definiowanie obsługiwanych typów zawartości żądania przy użyciu atrybutu użycia](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="15853-339">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="15853-340">Aby uzyskać pełny opis szablonów tras i powiązanych opcji, zobacz [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="15853-340">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="15853-341">Aby uzyskać więcej informacji na temat `[ApiController]` , zobacz [ApiController Attribute](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="15853-341">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="15853-342">Nazwa trasy</span><span class="sxs-lookup"><span data-stu-id="15853-342">Route name</span></span>

<span data-ttu-id="15853-343">Następujący kod definiuje nazwę trasy `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="15853-343">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="15853-344">Nazwy tras mogą służyć do generowania adresów URL na podstawie określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-344">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="15853-345">Nazwy tras:</span><span class="sxs-lookup"><span data-stu-id="15853-345">Route names:</span></span>

* <span data-ttu-id="15853-346">Nie ma wpływu na zachowanie w zakresie routingu zgodnego z adresem URL.</span><span class="sxs-lookup"><span data-stu-id="15853-346">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="15853-347">Są używane tylko na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-347">Are only used for URL generation.</span></span>

<span data-ttu-id="15853-348">Nazwy tras muszą być unikatowe w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-348">Route names must be unique application-wide.</span></span>

<span data-ttu-id="15853-349">Powyższy kod przy użyciu konwencjonalnej trasy domyślnej, która definiuje `id` parametr jako opcjonalny ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="15853-349">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="15853-350">Możliwość precyzyjnego określania interfejsów API ma zalety, takie jak umożliwienie `/products` i `/products/5` wysyłanie ich do różnych akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-350">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="15853-351">Łączenie tras atrybutów</span><span class="sxs-lookup"><span data-stu-id="15853-351">Combining attribute routes</span></span>

<span data-ttu-id="15853-352">Aby mniej powtarzać Routing atrybutów, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-352">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="15853-353">Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzone w celu rozesłania szablonów w akcjach.</span><span class="sxs-lookup"><span data-stu-id="15853-353">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="15853-354">Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w kontrolerze używają routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-354">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="15853-355">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="15853-355">In the preceding example:</span></span>

* <span data-ttu-id="15853-356">Ścieżka adresu URL `/products` może być taka sama`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="15853-356">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="15853-357">Ścieżka adresu URL `/products/5` może być taka sama `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="15853-357">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="15853-358">Obie te akcje pasują tylko do protokołu HTTP, `GET` ponieważ są oznaczone `[HttpGet]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="15853-358">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="15853-359">Szablony tras zastosowane do akcji rozpoczynającej się od `/` lub `~/` nie są połączone z szablonami tras zastosowanymi do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-359">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="15853-360">Poniższy przykład dopasowuje zestaw ścieżek adresów URL podobny do trasy domyślnej.</span><span class="sxs-lookup"><span data-stu-id="15853-360">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="15853-361">W poniższej tabeli opisano `[Route]` atrybuty w poprzednim kodzie:</span><span class="sxs-lookup"><span data-stu-id="15853-361">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="15853-362">Atrybut</span><span class="sxs-lookup"><span data-stu-id="15853-362">Attribute</span></span>               | <span data-ttu-id="15853-363">Łączy z`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="15853-363">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="15853-364">Definiuje szablon trasy</span><span class="sxs-lookup"><span data-stu-id="15853-364">Defines route template</span></span> |
| ---
<span data-ttu-id="15853-365">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-365">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-366">'Blazor'</span></span>
- <span data-ttu-id="15853-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-367">'Identity'</span></span>
- <span data-ttu-id="15853-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-369">'Razor'</span></span>
- <span data-ttu-id="15853-370">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="15853-371">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-371">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-372">'Blazor'</span></span>
- <span data-ttu-id="15853-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-373">'Identity'</span></span>
- <span data-ttu-id="15853-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-375">'Razor'</span></span>
- <span data-ttu-id="15853-376">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="15853-377">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-377">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-378">'Blazor'</span></span>
- <span data-ttu-id="15853-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-379">'Identity'</span></span>
- <span data-ttu-id="15853-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-381">'Razor'</span></span>
- <span data-ttu-id="15853-382">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="15853-383">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-383">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-384">'Blazor'</span></span>
- <span data-ttu-id="15853-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-385">'Identity'</span></span>
- <span data-ttu-id="15853-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-387">'Razor'</span></span>
- <span data-ttu-id="15853-388">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="15853-389">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-389">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-390">'Blazor'</span></span>
- <span data-ttu-id="15853-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-391">'Identity'</span></span>
- <span data-ttu-id="15853-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-393">'Razor'</span></span>
- <span data-ttu-id="15853-394">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="15853-395">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-395">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-396">'Blazor'</span></span>
- <span data-ttu-id="15853-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-397">'Identity'</span></span>
- <span data-ttu-id="15853-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-399">'Razor'</span></span>
- <span data-ttu-id="15853-400">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-400">'SignalR' uid:</span></span> 

<span data-ttu-id="15853-401">--------- | ---title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-401">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-402">'Blazor'</span></span>
- <span data-ttu-id="15853-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-403">'Identity'</span></span>
- <span data-ttu-id="15853-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-405">'Razor'</span></span>
- <span data-ttu-id="15853-406">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="15853-407">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-407">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-408">'Blazor'</span></span>
- <span data-ttu-id="15853-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-409">'Identity'</span></span>
- <span data-ttu-id="15853-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-411">'Razor'</span></span>
- <span data-ttu-id="15853-412">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="15853-413">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-413">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-414">'Blazor'</span></span>
- <span data-ttu-id="15853-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-415">'Identity'</span></span>
- <span data-ttu-id="15853-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-417">'Razor'</span></span>
- <span data-ttu-id="15853-418">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="15853-419">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-419">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-420">'Blazor'</span></span>
- <span data-ttu-id="15853-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-421">'Identity'</span></span>
- <span data-ttu-id="15853-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-423">'Razor'</span></span>
- <span data-ttu-id="15853-424">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-424">'SignalR' uid:</span></span> 

<span data-ttu-id="15853-425">------ | ---title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-425">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-426">'Blazor'</span></span>
- <span data-ttu-id="15853-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-427">'Identity'</span></span>
- <span data-ttu-id="15853-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-429">'Razor'</span></span>
- <span data-ttu-id="15853-430">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="15853-431">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="15853-431">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="15853-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="15853-432">'Blazor'</span></span>
- <span data-ttu-id="15853-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="15853-433">'Identity'</span></span>
- <span data-ttu-id="15853-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="15853-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="15853-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="15853-435">'Razor'</span></span>
- <span data-ttu-id="15853-436">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="15853-436">'SignalR' uid:</span></span> 

<span data-ttu-id="15853-437">----- | | `[Route("")]` | Tak | `"Home"` |
| `[Route("Index")]` | Tak | `"Home/Index"` |
| `[Route("/")]` | **Nie** | `""` |
 | `[Route("About")]` | Tak | `"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="15853-437">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="15853-438">Porządek trasy dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="15853-438">Attribute route order</span></span>

<span data-ttu-id="15853-439">Routing kompiluje drzewo i dopasowuje wszystkie punkty końcowe jednocześnie:</span><span class="sxs-lookup"><span data-stu-id="15853-439">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="15853-440">Wpisy trasy zachowują się tak, jakby zostały umieszczone w idealnej kolejności.</span><span class="sxs-lookup"><span data-stu-id="15853-440">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="15853-441">Najbardziej konkretne trasy mają możliwość wykonania przed bardziej ogólnymi trasami.</span><span class="sxs-lookup"><span data-stu-id="15853-441">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="15853-442">Na przykład, podobnie jak w przypadku `blog/search/{topic}` bardziej szczegółowych informacji niż trasie atrybutu `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="15853-442">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="15853-443">`blog/search/{topic}`Trasa ma wyższy priorytet, ponieważ jest domyślnie bardziej specyficzna.</span><span class="sxs-lookup"><span data-stu-id="15853-443">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="15853-444">Przy użyciu [konwencjonalnego routingu](#cr)deweloper jest odpowiedzialny za umieszczanie tras w odpowiedniej kolejności.</span><span class="sxs-lookup"><span data-stu-id="15853-444">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="15853-445">Trasy atrybutów mogą konfigurować zamówienie przy użyciu <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> właściwości.</span><span class="sxs-lookup"><span data-stu-id="15853-445">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="15853-446">Wszystkie [atrybuty trasy](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) dostarczonej przez platformę obejmują `Order` .</span><span class="sxs-lookup"><span data-stu-id="15853-446">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="15853-447">Trasy są przetwarzane zgodnie z rosnącą sortowaniem `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="15853-447">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="15853-448">Kolejność domyślna to `0` .</span><span class="sxs-lookup"><span data-stu-id="15853-448">The default order is `0`.</span></span> <span data-ttu-id="15853-449">Ustawianie trasy przy użyciu `Order = -1` przebiegów przed trasami, które nie ustawiają kolejności.</span><span class="sxs-lookup"><span data-stu-id="15853-449">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="15853-450">Ustawianie trasy przy użyciu `Order = 1` przebiegu po domyślnej kolejności tras.</span><span class="sxs-lookup"><span data-stu-id="15853-450">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="15853-451">**Należy unikać** w zależności od `Order` .</span><span class="sxs-lookup"><span data-stu-id="15853-451">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="15853-452">Jeśli przestrzeń adresów URL aplikacji wymaga jawnych wartości kolejności, aby można było prawidłowo kierować trasy, prawdopodobnie również jest myląca dla klientów.</span><span class="sxs-lookup"><span data-stu-id="15853-452">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="15853-453">Ogólnie rzecz biorąc, routing atrybutów wybiera prawidłową trasę z dopasowywaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-453">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="15853-454">Jeśli domyślna kolejność generowania adresów URL nie działa, użycie nazwy trasy jako przesłonięcia jest zwykle prostsze niż stosowanie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="15853-454">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="15853-455">Należy wziąć pod uwagę następujące dwa kontrolery, które definiują trasę zgodną z `/home` :</span><span class="sxs-lookup"><span data-stu-id="15853-455">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="15853-456">Żądanie `/home` z powyższym kodem zgłasza wyjątek podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="15853-456">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="15853-457">Dodanie `Order` do jednego z atrybutów trasy rozwiązuje niejednoznaczność:</span><span class="sxs-lookup"><span data-stu-id="15853-457">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="15853-458">Za pomocą powyższego kodu program `/home` uruchamia `HomeController.Index` punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="15853-458">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="15853-459">, Aby przejść do `MyDemoController.MyIndex` żądania `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="15853-459">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="15853-460">**Uwaga**:</span><span class="sxs-lookup"><span data-stu-id="15853-460">**Note**:</span></span>

* <span data-ttu-id="15853-461">Poprzedni kod jest przykładem lub słabym projektem routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-461">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="15853-462">Została użyta do zilustrowania `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="15853-462">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="15853-463">`Order`Właściwość rozwiązuje tylko niejednoznaczność, ale nie można dopasować tego szablonu.</span><span class="sxs-lookup"><span data-stu-id="15853-463">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="15853-464">Lepszym rozwiązaniem jest usunięcie `[Route("Home")]` szablonu.</span><span class="sxs-lookup"><span data-stu-id="15853-464">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="15853-465">Zobacz [ Razor strony trasy i konwencje aplikacji: kolejność tras](xref:razor-pages/razor-pages-conventions#route-order) na potrzeby informacji o kolejności tras na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="15853-465">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="15853-466">W niektórych przypadkach błąd HTTP 500 jest zwracany z niejednoznacznych tras.</span><span class="sxs-lookup"><span data-stu-id="15853-466">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="15853-467">Użyj [rejestrowania](xref:fundamentals/logging/index) , aby zobaczyć, które punkty końcowe spowodowały `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="15853-467">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="15853-468">Zastępowanie tokenu w szablonach tras [Controller], [Action], [obszar]</span><span class="sxs-lookup"><span data-stu-id="15853-468">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="15853-469">Dla wygody atrybut trasy obsługują zastępowanie tokenów dla zarezerwowanych parametrów trasy przez załączanie tokenu w jednym z następujących:</span><span class="sxs-lookup"><span data-stu-id="15853-469">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="15853-470">Nawiasy kwadratowe:`[]`</span><span class="sxs-lookup"><span data-stu-id="15853-470">Square brackets: `[]`</span></span>
* <span data-ttu-id="15853-471">Nawiasy klamrowe:`{}`</span><span class="sxs-lookup"><span data-stu-id="15853-471">Curly braces: `{}`</span></span>

<span data-ttu-id="15853-472">Tokeny `[action]` , `[area]` i `[controller]` są zastępowane wartościami nazwy akcji, obszaru i nazwy kontrolera z akcji, w której zdefiniowano trasę:</span><span class="sxs-lookup"><span data-stu-id="15853-472">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="15853-473">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="15853-473">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="15853-474">Prawdopodobny`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="15853-474">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="15853-475">Prawdopodobny`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="15853-475">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="15853-476">Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-476">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="15853-477">Poprzedni przykład zachowuje się tak samo jak w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="15853-477">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="15853-478">Trasy atrybutu można także łączyć z dziedziczeniem.</span><span class="sxs-lookup"><span data-stu-id="15853-478">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="15853-479">Jest to zaawansowane połączenie z zastępowaniem tokenu.</span><span class="sxs-lookup"><span data-stu-id="15853-479">This is powerful combined with token replacement.</span></span> <span data-ttu-id="15853-480">Zastępowanie tokenu dotyczy również nazw tras zdefiniowanych przez trasy atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-480">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="15853-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji:</span><span class="sxs-lookup"><span data-stu-id="15853-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="15853-482">Zastępowanie tokenu dotyczy również nazw tras zdefiniowanych przez trasy atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-482">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="15853-483">generuje unikatową nazwę trasy dla każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-483">generates a unique route name for each action.</span></span>

<span data-ttu-id="15853-484">Aby dopasować ogranicznik zamiany tokenu literału `[` lub `]` , należy to zrobić, powtarzając znak ( `[[` lub `]]` ).</span><span class="sxs-lookup"><span data-stu-id="15853-484">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="15853-485">Używanie transformatora parametrów do dostosowywania zastępowania tokenu</span><span class="sxs-lookup"><span data-stu-id="15853-485">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="15853-486">Zastępowanie tokenu można dostosować za pomocą transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="15853-486">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="15853-487">Transformator parametrów implementuje <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="15853-487">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="15853-488">Na przykład niestandardowy `SlugifyParameterTransformer` transformator parametrów zmienia `SubscriptionManagement` wartość trasy na `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="15853-488">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="15853-489"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention>Jest konwencją modelu aplikacji, która:</span><span class="sxs-lookup"><span data-stu-id="15853-489">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="15853-490">Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-490">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="15853-491">Dostosowuje wartości tokenów tras w miarę ich wymiany.</span><span class="sxs-lookup"><span data-stu-id="15853-491">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="15853-492">Poprzednia `ListAll` Metoda pasuje `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="15853-492">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="15853-493">`RouteTokenTransformerConvention`Jest zarejestrowany jako opcja w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="15853-493">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="15853-494">Zobacz [powiadomienia MDN Web docs](https://developer.mozilla.org/docs/Glossary/Slug) , aby zapoznać się z definicją informacji o sobie.</span><span class="sxs-lookup"><span data-stu-id="15853-494">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="15853-495">Wiele tras atrybutów</span><span class="sxs-lookup"><span data-stu-id="15853-495">Multiple attribute routes</span></span>

<span data-ttu-id="15853-496">Routing atrybutów obsługuje definiowanie wielu tras, które docierają do tej samej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-496">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="15853-497">Najczęstszym sposobem użycia tej metody jest naśladowanie zachowania domyślnej trasy konwencjonalnej, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="15853-497">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="15853-498">Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każda z nich łączy się z każdym z atrybutów tras w metodach akcji:</span><span class="sxs-lookup"><span data-stu-id="15853-498">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="15853-499">Wszystkie ograniczenia trasy [http zlecenia](#verb) są implementowane `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="15853-499">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="15853-500">Gdy wiele atrybutów trasy, które implementują <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> są umieszczane w akcji:</span><span class="sxs-lookup"><span data-stu-id="15853-500">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="15853-501">Każde ograniczenie akcji łączy się z szablonem trasy zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-501">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="15853-502">Korzystanie z wielu tras w akcjach może wydawać się użyteczne i zaawansowane, dlatego lepiej jest zachować podstawową i dobrze zdefiniowaną przestrzeń adresów URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-502">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="15853-503">Użyj wielu tras w przypadku akcji **tylko wtedy** , gdy jest to konieczne, na przykład w celu obsługi istniejących klientów.</span><span class="sxs-lookup"><span data-stu-id="15853-503">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="15853-504">Określanie opcjonalnych parametrów trasy, wartości domyślnych i ograniczeń</span><span class="sxs-lookup"><span data-stu-id="15853-504">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="15853-505">Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="15853-505">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="15853-506">W poprzednim kodzie, `[HttpPost("product/{id:int}")]` stosuje ograniczenie trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-506">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="15853-507">`ProductsController.ShowProduct`Akcja jest dopasowywana tylko przez ścieżki URL, takie jak `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="15853-507">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="15853-508">Część szablonu trasy `{id:int}` ogranicza ten segment tylko do liczb całkowitych.</span><span class="sxs-lookup"><span data-stu-id="15853-508">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="15853-509">Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="15853-509">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="15853-510">Niestandardowe atrybuty trasy przy użyciu IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="15853-510">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="15853-511">Wszystkie [atrybuty trasy](#rt) implementują <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="15853-511">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="15853-512">Środowisko uruchomieniowe ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="15853-512">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="15853-513">Wyszukuje atrybuty klas kontrolera i metod akcji podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-513">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="15853-514">Używa atrybutów, które implementują `IRouteTemplateProvider` w celu utworzenia początkowego zestawu tras.</span><span class="sxs-lookup"><span data-stu-id="15853-514">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="15853-515">Zaimplementuj `IRouteTemplateProvider` , aby zdefiniować niestandardowe atrybuty trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-515">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="15853-516">Każdy `IRouteTemplateProvider` z nich umożliwia definiowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:</span><span class="sxs-lookup"><span data-stu-id="15853-516">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="15853-517">Poprzednia `Get` Metoda zwraca `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="15853-517">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="15853-518">Dostosowywanie tras atrybutów przy użyciu modelu aplikacji</span><span class="sxs-lookup"><span data-stu-id="15853-518">Use application model to customize attribute routes</span></span>

<span data-ttu-id="15853-519">Model aplikacji:</span><span class="sxs-lookup"><span data-stu-id="15853-519">The application model:</span></span>

* <span data-ttu-id="15853-520">Jest modelem obiektu utworzonym podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="15853-520">Is an object model created at startup.</span></span>
* <span data-ttu-id="15853-521">Zawiera wszystkie metadane używane przez ASP.NET Core do kierowania i wykonywania akcji w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-521">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="15853-522">Model aplikacji zawiera wszystkie dane zebrane z atrybutów tras.</span><span class="sxs-lookup"><span data-stu-id="15853-522">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="15853-523">Dane z atrybutów trasy są udostępniane przez `IRouteTemplateProvider` implementację.</span><span class="sxs-lookup"><span data-stu-id="15853-523">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="15853-524">Obowiązujący</span><span class="sxs-lookup"><span data-stu-id="15853-524">Conventions:</span></span>

* <span data-ttu-id="15853-525">Można napisać, aby zmodyfikować model aplikacji, aby dostosować sposób zachowania routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-525">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="15853-526">Są odczytywane podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-526">Are read at app startup.</span></span>

<span data-ttu-id="15853-527">W tej sekcji przedstawiono podstawowy przykład dostosowywania routingu przy użyciu modelu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-527">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="15853-528">Poniższy kod sprawia, że trasy są w przybliżeniu zgodne ze strukturą folderów projektu.</span><span class="sxs-lookup"><span data-stu-id="15853-528">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="15853-529">Poniższy kod uniemożliwia `namespace` stosowanie Konwencji do kontrolerów, które są kierowane przez atrybut:</span><span class="sxs-lookup"><span data-stu-id="15853-529">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="15853-530">Na przykład następujący kontroler nie używa `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="15853-530">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="15853-531">`NamespaceRoutingConvention.Apply`Metoda:</span><span class="sxs-lookup"><span data-stu-id="15853-531">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="15853-532">Nie robi nic, Jeśli kontroler ma przypisany atrybut.</span><span class="sxs-lookup"><span data-stu-id="15853-532">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="15853-533">Ustawia szablon szablonu oparty na `namespace` , z `namespace` usuniętym podstawowym.</span><span class="sxs-lookup"><span data-stu-id="15853-533">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="15853-534">`NamespaceRoutingConvention`Można zastosować w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="15853-534">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="15853-535">Rozważmy na przykład następujący kontroler:</span><span class="sxs-lookup"><span data-stu-id="15853-535">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="15853-536">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="15853-536">In the preceding code:</span></span>

* <span data-ttu-id="15853-537">Podstawą `namespace` jest `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="15853-537">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="15853-538">Pełna nazwa poprzedniego kontrolera to `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="15853-538">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="15853-539">`NamespaceRoutingConvention`Ustawia szablon controllers na `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="15853-539">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="15853-540">`NamespaceRoutingConvention`Można również zastosować jako atrybut na kontrolerze:</span><span class="sxs-lookup"><span data-stu-id="15853-540">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="15853-541">Routing mieszany: Routing atrybutów a konwencjonalny Routing</span><span class="sxs-lookup"><span data-stu-id="15853-541">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="15853-542">Aplikacje ASP.NET Core mogą łączyć użycie konwencjonalnych routingu i routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-542">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="15853-543">Typowym zastosowaniem są trasy konwencjonalne dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.</span><span class="sxs-lookup"><span data-stu-id="15853-543">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="15853-544">Akcje są albo podkierowane do Konwencji lub kierowane przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="15853-544">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="15853-545">Umieszczenie trasy na kontrolerze lub akcja powoduje, że atrybut jest kierowany.</span><span class="sxs-lookup"><span data-stu-id="15853-545">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="15853-546">Akcje definiujące trasy atrybutów nie mogą być osiągane za pomocą konwencjonalnych tras i vice versa.</span><span class="sxs-lookup"><span data-stu-id="15853-546">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="15853-547">**Każdy** atrybut trasy na kontrolerze powoduje kierowanie **wszystkich** akcji w atrybucie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-547">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="15853-548">Routing atrybutów i Routing konwencjonalny używają tego samego aparatu routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-548">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="15853-549">Generowanie adresów URL i wartości otoczenia</span><span class="sxs-lookup"><span data-stu-id="15853-549">URL Generation and ambient values</span></span>

<span data-ttu-id="15853-550">Aplikacje mogą używać funkcji generowania adresów URL routingu do generowania linków URL do akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-550">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="15853-551">Generowanie adresów URL eliminuje adresy URL zakodowana, co sprawia, że kod jest bardziej niezawodny i konserwowany.</span><span class="sxs-lookup"><span data-stu-id="15853-551">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="15853-552">Ta sekcja koncentruje się na funkcjach generowania adresów URL udostępnianych przez MVC i obejmuje jedynie podstawowe informacje na temat sposobu działania generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-552">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="15853-553">Aby uzyskać szczegółowy opis generowania adresów URL, zobacz temat [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="15853-553">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="15853-554"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Interfejs jest podstawowym elementem infrastruktury między MVC i Routing na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-554">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="15853-555">Wystąpienie `IUrlHelper` jest dostępne za pomocą `Url` właściwości w kontrolerach, widokach i składnikach widoku.</span><span class="sxs-lookup"><span data-stu-id="15853-555">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="15853-556">W poniższym przykładzie `IUrlHelper` interfejs jest używany przez `Controller.Url` Właściwość w celu WYGENEROWANIA adresu URL dla innej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-556">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="15853-557">Jeśli aplikacja używa domyślnej trasy konwencjonalnej, wartość `url` zmiennej jest ciągiem ścieżki URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="15853-557">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="15853-558">Ta ścieżka URL jest tworzona przez połączenie za pomocą routingu:</span><span class="sxs-lookup"><span data-stu-id="15853-558">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="15853-559">Wartości trasy z bieżącego żądania, które są nazywane **wartościami otoczenia**.</span><span class="sxs-lookup"><span data-stu-id="15853-559">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="15853-560">Wartości przesyłane do `Url.Action` i podstawiające te wartości do szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="15853-560">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="15853-561">Każdy parametr trasy w szablonie trasy ma swoją wartość zastępowaną przez pasujące nazwy wartościami i wartościami otoczenia.</span><span class="sxs-lookup"><span data-stu-id="15853-561">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="15853-562">Parametr trasy, który nie ma wartości, może:</span><span class="sxs-lookup"><span data-stu-id="15853-562">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="15853-563">Użyj wartości domyślnej, jeśli ma ją.</span><span class="sxs-lookup"><span data-stu-id="15853-563">Use a default value if it has one.</span></span>
* <span data-ttu-id="15853-564">Można pominąć, jeśli jest to opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="15853-564">Be skipped if it's optional.</span></span> <span data-ttu-id="15853-565">Na przykład, `id` z szablonu trasy `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="15853-565">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="15853-566">Generowanie adresu URL kończy się niepowodzeniem, jeśli którykolwiek z wymaganych parametrów trasy nie ma odpowiadającej mu wartości.</span><span class="sxs-lookup"><span data-stu-id="15853-566">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="15853-567">Jeśli generowanie adresów URL kończy się niepowodzeniem dla trasy, kolejna trasa zostanie ponowiona do momentu przetworzenia wszystkich tras lub znalezienia dopasowania.</span><span class="sxs-lookup"><span data-stu-id="15853-567">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="15853-568">W poprzednim przykładzie `Url.Action` przyjęto założenie [konwencjonalnego routingu](#cr).</span><span class="sxs-lookup"><span data-stu-id="15853-568">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="15853-569">Generowanie adresów URL działa podobnie jak w przypadku [routingu atrybutów](#ar), chociaż koncepcje różnią się.</span><span class="sxs-lookup"><span data-stu-id="15853-569">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="15853-570">Z routingiem konwencjonalnym:</span><span class="sxs-lookup"><span data-stu-id="15853-570">With conventional routing:</span></span>

* <span data-ttu-id="15853-571">Wartości trasy służą do rozszerzania szablonu.</span><span class="sxs-lookup"><span data-stu-id="15853-571">The route values are used to expand a template.</span></span>
* <span data-ttu-id="15853-572">Wartości trasy dla `controller` i `action` zwykle pojawiają się w tym szablonie.</span><span class="sxs-lookup"><span data-stu-id="15853-572">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="15853-573">Dzieje się tak, ponieważ adresy URL dopasowane przez Routing są zgodne z Konwencją.</span><span class="sxs-lookup"><span data-stu-id="15853-573">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="15853-574">W poniższym przykładzie zastosowano Routing atrybutów:</span><span class="sxs-lookup"><span data-stu-id="15853-574">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="15853-575">`Source`Akcja w poprzednim kodzie generuje `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="15853-575">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="15853-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>został dodany w ASP.NET Core 3,0 jako alternatywa dla `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="15853-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="15853-577">`LinkGenerator`oferuje podobne, ale bardziej elastyczne funkcje.</span><span class="sxs-lookup"><span data-stu-id="15853-577">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="15853-578">Każda metoda w systemie `IUrlHelper` ma również odpowiednią rodzinę metod `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="15853-578">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="15853-579">Generowanie adresów URL według nazwy akcji</span><span class="sxs-lookup"><span data-stu-id="15853-579">Generating URLs by action name</span></span>

<span data-ttu-id="15853-580">Wartość [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)i wszystkie powiązane przeciążenia są przeznaczone do generowania docelowego punktu końcowego przez określenie nazwy kontrolera i nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-580">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="15853-581">W przypadku używania `Url.Action` , bieżąca wartość trasy dla `controller` i `action` jest udostępniana przez środowisko uruchomieniowe:</span><span class="sxs-lookup"><span data-stu-id="15853-581">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="15853-582">Wartość `controller` i `action` jest częścią zarówno [wartości otoczenia](#ambient) , jak i wartości.</span><span class="sxs-lookup"><span data-stu-id="15853-582">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="15853-583">Metoda `Url.Action` zawsze używa bieżących wartości `action` i `controller` i generuje ścieżkę URL, która jest przesyłana do bieżącej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-583">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="15853-584">Funkcja routingu próbuje użyć wartości w otoczeniu wartości, aby podać informacje, które nie zostały dostarczone podczas generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-584">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="15853-585">Rozważ użycie trasy podobnej do `{a}/{b}/{c}/{d}` wartości otoczenia `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="15853-585">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="15853-586">Routing ma wystarczającą ilość informacji do wygenerowania adresu URL bez dodatkowych wartości.</span><span class="sxs-lookup"><span data-stu-id="15853-586">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="15853-587">Routing ma wystarczającą ilość informacji, ponieważ wszystkie parametry tras mają wartość.</span><span class="sxs-lookup"><span data-stu-id="15853-587">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="15853-588">Jeśli wartość `{ d = Donovan }` zostanie dodana:</span><span class="sxs-lookup"><span data-stu-id="15853-588">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="15853-589">Wartość `{ d = David }` jest ignorowana.</span><span class="sxs-lookup"><span data-stu-id="15853-589">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="15853-590">Wygenerowana Ścieżka adresu URL to `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="15853-590">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="15853-591">**Ostrzeżenie**: ścieżki URL są hierarchiczne.</span><span class="sxs-lookup"><span data-stu-id="15853-591">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="15853-592">W poprzednim przykładzie, jeśli wartość `{ c = Cheryl }` jest dodawana:</span><span class="sxs-lookup"><span data-stu-id="15853-592">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="15853-593">Obie wartości `{ c = Carol, d = David }` są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="15853-593">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="15853-594">Nie jest już dostępna wartość `d` i generowanie adresu URL nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="15853-594">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="15853-595">Wymagane wartości `c` i `d` muszą być określone w celu WYGENEROWANIA adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-595">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="15853-596">Prawdopodobnie wystąpił problem z domyślną trasą `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="15853-596">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="15853-597">Ten problem występuje rzadko `Url.Action` , ponieważ zawsze jawnie określa `controller` `action` wartość i.</span><span class="sxs-lookup"><span data-stu-id="15853-597">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="15853-598">Kilka przeciążeń [adresu URL. akcja](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) przyjmuje obiekt wartości trasy, aby podać wartości parametrów trasy innych niż `controller` i `action` .</span><span class="sxs-lookup"><span data-stu-id="15853-598">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="15853-599">Obiekt wartości trasy jest często używany z `id` .</span><span class="sxs-lookup"><span data-stu-id="15853-599">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="15853-600">Na przykład `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="15853-600">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="15853-601">Obiekt wartości trasy:</span><span class="sxs-lookup"><span data-stu-id="15853-601">The route values object:</span></span>

* <span data-ttu-id="15853-602">Według Konwencji jest zwykle obiektem typu anonimowego.</span><span class="sxs-lookup"><span data-stu-id="15853-602">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="15853-603">Może to być `IDictionary<>` lub [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="15853-603">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="15853-604">Wszystkie dodatkowe wartości trasy, które nie pasują do parametrów trasy, są umieszczane w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="15853-604">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="15853-605">Poprzedni kod generuje `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="15853-605">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="15853-606">Poniższy kod generuje bezwzględny adres URL:</span><span class="sxs-lookup"><span data-stu-id="15853-606">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="15853-607">Aby utworzyć bezwzględny adres URL, użyj jednego z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="15853-607">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="15853-608">Przeciążenie, które akceptuje `protocol` .</span><span class="sxs-lookup"><span data-stu-id="15853-608">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="15853-609">Na przykład poprzedzający kod.</span><span class="sxs-lookup"><span data-stu-id="15853-609">For example, the preceding code.</span></span>
* <span data-ttu-id="15853-610">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), który domyślnie generuje bezwzględne identyfikatory URI.</span><span class="sxs-lookup"><span data-stu-id="15853-610">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="15853-611">Generuj adresy URL według trasy</span><span class="sxs-lookup"><span data-stu-id="15853-611">Generate URLs by route</span></span>

<span data-ttu-id="15853-612">Poprzedni kod wykazał wygenerowanie adresu URL przez przekazanie go do kontrolera i nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-612">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="15853-613">`IUrlHelper`zapewnia także rodzinę [adresów URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="15853-613">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="15853-614">Te metody są podobne do [adresu URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nie kopiują bieżących wartości `action` i `controller` do wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-614">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="15853-615">Najczęstsze użycie `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="15853-615">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="15853-616">Określa nazwę trasy do wygenerowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-616">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="15853-617">Zwykle nie określa kontrolera ani nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-617">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="15853-618">Następujący Razor plik generuje link HTML do `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="15853-618">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="15853-619">Generuj adresy URL w kodzie HTML iRazor</span><span class="sxs-lookup"><span data-stu-id="15853-619">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="15853-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>udostępnia <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) oraz [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) do wygenerowania `<form>` i `<a>` elementów.</span><span class="sxs-lookup"><span data-stu-id="15853-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="15853-621">Metody te używają metody [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) do generowania adresu URL i akceptują podobne argumenty.</span><span class="sxs-lookup"><span data-stu-id="15853-621">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="15853-622">`Url.RouteUrl`Pomocników dla programu `HtmlHelper` mają `Html.BeginRouteForm` `Html.RouteLink` podobną funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="15853-622">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="15853-623">TagHelpers Generuj adresy URL za pomocą `form` TagHelper i `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="15853-623">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="15853-624">Oba te zastosowania `IUrlHelper` dla ich implementacji.</span><span class="sxs-lookup"><span data-stu-id="15853-624">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="15853-625">Aby uzyskać więcej informacji, zobacz [pomocników tagów w formularzach](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="15853-625">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="15853-626">W widokach `IUrlHelper` można korzystać z `Url` właściwości dla dowolnej generacji adresów URL ad hoc, która nie jest objęta powyższym.</span><span class="sxs-lookup"><span data-stu-id="15853-626">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="15853-627">Generowanie adresów URL w wynikach akcji</span><span class="sxs-lookup"><span data-stu-id="15853-627">URL generation in Action Results</span></span>

<span data-ttu-id="15853-628">Poprzednie przykłady pokazano przy użyciu `IUrlHelper` w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="15853-628">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="15853-629">Najczęstszym sposobem użycia na kontrolerze jest wygenerowanie adresu URL jako części wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-629">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="15853-630"><xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> Klasy bazowe i zapewniają wygodne metody dla wyników akcji, które odwołują się do innej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-630">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="15853-631">Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika:</span><span class="sxs-lookup"><span data-stu-id="15853-631">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="15853-632">Wynikiem akcji są metody fabryki, takie jak <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> i, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> postępuj zgodnie z podobnym wzorcem do metod w `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="15853-632">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="15853-633">Specjalny przypadek dla dedykowanych tras konwencjonalnych</span><span class="sxs-lookup"><span data-stu-id="15853-633">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="15853-634">Funkcja [routingu konwencjonalnego](#cr) może używać specjalnego rodzaju definicji trasy o nazwie [dedykowanej, konwencjonalnej trasy](#dcr).</span><span class="sxs-lookup"><span data-stu-id="15853-634">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="15853-635">W poniższym przykładzie trasa o nazwie `blog` jest dedykowaną konwencjonalną trasą:</span><span class="sxs-lookup"><span data-stu-id="15853-635">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="15853-636">Przy użyciu powyższych definicji trasy program `Url.Action("Index", "Home")` generuje ścieżkę URL `/` przy użyciu `default` trasy, ale dlaczego?</span><span class="sxs-lookup"><span data-stu-id="15853-636">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="15853-637">Możliwe jest odpuszczenie, że wartości trasy `{ controller = Home, action = Index }` byłyby wystarczające do wygenerowania adresu URL przy użyciu `blog` , a wynikiem będzie `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="15853-637">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="15853-638">[Dedykowane konwencjonalne trasy](#dcr) polegają na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiadającego parametru trasy, który uniemożliwia zbyt [zachłanne](xref:fundamentals/routing#greedy) trasy z generowaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-638">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="15853-639">W takim przypadku wartości domyślne to `{ controller = Blog, action = Article }` , a ani nie `controller` `action` pojawiają się jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-639">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="15853-640">Gdy Routing wykonuje generowanie adresów URL, podane wartości muszą być zgodne z wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="15853-640">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="15853-641">Generowanie adresu URL `blog` nie powiodło się, ponieważ wartości `{ controller = Home, action = Index }` nie są zgodne `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="15853-641">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="15853-642">Następnie usługa routingu wraca do wypróbowania `default` , która się powiedzie.</span><span class="sxs-lookup"><span data-stu-id="15853-642">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="15853-643">Obszary</span><span class="sxs-lookup"><span data-stu-id="15853-643">Areas</span></span>

<span data-ttu-id="15853-644">[Obszary](xref:mvc/controllers/areas) są funkcją MVC służącą do organizowania powiązanych funkcji w grupie jako oddzielnej:</span><span class="sxs-lookup"><span data-stu-id="15853-644">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="15853-645">Przestrzeń nazw routingu dla akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-645">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="15853-646">Struktura folderów dla widoków.</span><span class="sxs-lookup"><span data-stu-id="15853-646">Folder structure for views.</span></span>

<span data-ttu-id="15853-647">Użycie obszarów umożliwia aplikacji posiadanie wielu kontrolerów o tej samej nazwie, o ile mają one różne obszary.</span><span class="sxs-lookup"><span data-stu-id="15853-647">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="15853-648">Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie kolejnego parametru trasy `area` do `controller` i `action` .</span><span class="sxs-lookup"><span data-stu-id="15853-648">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="15853-649">W tej sekcji omówiono sposób, w jaki Routing współdziała z obszarami.</span><span class="sxs-lookup"><span data-stu-id="15853-649">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="15853-650">Zobacz [obszary](xref:mvc/controllers/areas) , aby uzyskać szczegółowe informacje na temat sposobu używania obszarów w widokach.</span><span class="sxs-lookup"><span data-stu-id="15853-650">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="15853-651">Poniższy przykład konfiguruje MVC do używania domyślnej trasy konwencjonalnej i `area` trasy dla `area` nazwanego `Blog` :</span><span class="sxs-lookup"><span data-stu-id="15853-651">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="15853-652">W poprzednim kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> jest wywoływana w celu utworzenia `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="15853-652">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="15853-653">Drugi parametr, `"Blog"` , jest nazwą obszaru.</span><span class="sxs-lookup"><span data-stu-id="15853-653">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="15853-654">W przypadku dopasowania ścieżki URL podobnej `/Manage/Users/AddUser` do `"blog_route"` trasy generuje wartości trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="15853-654">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="15853-655">`area`Wartość trasy jest generowana przez wartość domyślną dla `area` .</span><span class="sxs-lookup"><span data-stu-id="15853-655">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="15853-656">Trasa utworzona przez `MapAreaControllerRoute` jest równoważna następującym:</span><span class="sxs-lookup"><span data-stu-id="15853-656">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="15853-657">`MapAreaControllerRoute`tworzy trasę przy użyciu wartości domyślnej i ograniczenia dotyczącej `area` użycia podanej nazwy obszaru, w tym przypadku `Blog` .</span><span class="sxs-lookup"><span data-stu-id="15853-657">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="15853-658">Wartość domyślna zapewnia, że trasa zawsze produkuje `{ area = Blog, ... }` , ograniczenie wymaga wartości `{ area = Blog, ... }` dla generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-658">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="15853-659">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="15853-659">Conventional routing is order-dependent.</span></span> <span data-ttu-id="15853-660">Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczone wcześniej, ponieważ są bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="15853-660">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="15853-661">Korzystając z powyższego przykładu, wartości trasy `{ area = Blog, controller = Users, action = AddUser }` są zgodne z następującą akcją:</span><span class="sxs-lookup"><span data-stu-id="15853-661">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="15853-662">Atrybut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) wskazuje, co oznacza kontroler w ramach obszaru.</span><span class="sxs-lookup"><span data-stu-id="15853-662">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="15853-663">Ten kontroler znajduje się w `Blog` obszarze.</span><span class="sxs-lookup"><span data-stu-id="15853-663">This controller is in the `Blog` area.</span></span> <span data-ttu-id="15853-664">Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru i **nie** są zgodne, gdy `area` wartość trasy jest dostarczana przez Routing.</span><span class="sxs-lookup"><span data-stu-id="15853-664">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="15853-665">W poniższym przykładzie tylko pierwszy kontroler może być zgodny z wartościami trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="15853-665">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="15853-666">Przestrzeń nazw każdego kontrolera jest pokazana w tym miejscu na potrzeby kompletności.</span><span class="sxs-lookup"><span data-stu-id="15853-666">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="15853-667">Jeśli powyższe kontrolery używają tego samego obszaru nazw, wygenerowany zostanie błąd kompilatora.</span><span class="sxs-lookup"><span data-stu-id="15853-667">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="15853-668">Przestrzenie nazw klas nie mają wpływu na Routing MVC.</span><span class="sxs-lookup"><span data-stu-id="15853-668">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="15853-669">Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko wtedy, gdy ich nazwa obszaru jest podana przez `area` wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-669">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="15853-670">Trzeci kontroler nie jest członkiem żadnego obszaru i można go dopasować tylko wtedy, gdy nie ma żadnej wartości parametru `area` Routing.</span><span class="sxs-lookup"><span data-stu-id="15853-670">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="15853-671">W warunkach pasujących do *nie ma żadnej*wartości, brak `area` wartości jest taka sama jak wartość `area` null lub pusty ciąg.</span><span class="sxs-lookup"><span data-stu-id="15853-671">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="15853-672">Podczas wykonywania akcji wewnątrz obszaru wartość trasy dla `area` jest dostępna jako [wartość otoczenia](#ambient) dla routingu do użycia na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-672">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="15853-673">Oznacza to, że domyślnie obszary programu *Sticky Notes* mają być używane do generowania adresów URL, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="15853-673">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="15853-674">Poniższy kod generuje adres URL `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="15853-674">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="15853-675">Definicja akcji</span><span class="sxs-lookup"><span data-stu-id="15853-675">Action definition</span></span>

<span data-ttu-id="15853-676">Metody publiczne na kontrolerze, z wyjątkiem tych z atrybutem nie będącym [akcją](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , są akcjami.</span><span class="sxs-lookup"><span data-stu-id="15853-676">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="15853-677">Przykładowy kod</span><span class="sxs-lookup"><span data-stu-id="15853-677">Sample code</span></span>

 * <span data-ttu-id="15853-678">Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) jest dołączana do [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) i służy do wyświetlania informacji o routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-678">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="15853-679">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="15853-679">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="15853-680">ASP.NET Core MVC używa [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) routingu, aby dopasować adresy URL żądań przychodzących i zmapować je na akcje.</span><span class="sxs-lookup"><span data-stu-id="15853-680">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="15853-681">Trasy są zdefiniowane w kodzie startowym lub atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-681">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="15853-682">Trasy opisują sposób dopasowywania ścieżek adresów URL do akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-682">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="15853-683">Trasy są również używane do generowania adresów URL (dla linków) wysyłanych w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="15853-683">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="15853-684">Akcje są albo podkierowane do Konwencji lub kierowane przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="15853-684">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="15853-685">Umieszczenie trasy na kontrolerze lub akcja powoduje, że atrybut jest kierowany.</span><span class="sxs-lookup"><span data-stu-id="15853-685">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="15853-686">Aby uzyskać więcej informacji, zobacz [Routing mieszany](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="15853-686">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="15853-687">Ten dokument wyjaśnia interakcje między MVC i routingiem oraz sposób, w jaki typowe aplikacje MVC używają funkcji routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-687">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="15853-688">Zobacz [Routing](xref:fundamentals/routing) , aby uzyskać szczegółowe informacje na temat routingu zaawansowanego.</span><span class="sxs-lookup"><span data-stu-id="15853-688">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="15853-689">Konfigurowanie oprogramowania pośredniczącego routingu</span><span class="sxs-lookup"><span data-stu-id="15853-689">Setting up Routing Middleware</span></span>

<span data-ttu-id="15853-690">W metodzie *konfigurowania* może zostać wyświetlony kod podobny do:</span><span class="sxs-lookup"><span data-stu-id="15853-690">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="15853-691">Wewnątrz wywołania do `UseMvc` , służy `MapRoute` do tworzenia pojedynczej trasy, którą będziemy odnosić jako `default` trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-691">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="15853-692">Większość aplikacji MVC będzie używać trasy z szablonem podobnym do `default` trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-692">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="15853-693">Szablon trasy `"{controller=Home}/{action=Index}/{id?}"` może być zgodny z ścieżką URL `/Products/Details/5` , tak jak i będzie wyodrębniał wartości trasy `{ controller = Products, action = Details, id = 5 }` przez tokenizowanie ścieżki.</span><span class="sxs-lookup"><span data-stu-id="15853-693">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="15853-694">MVC spróbuje zlokalizować kontroler o nazwie `ProductsController` i uruchomić akcję `Details` :</span><span class="sxs-lookup"><span data-stu-id="15853-694">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="15853-695">Należy zauważyć, że w tym przykładzie powiązanie modelu będzie używać wartości, `id = 5` Aby ustawić `id` parametr `5` podczas wywoływania tej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-695">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="15853-696">Aby uzyskać więcej informacji, zobacz [powiązanie modelu](../models/model-binding.md) .</span><span class="sxs-lookup"><span data-stu-id="15853-696">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="15853-697">Przy użyciu `default` trasy:</span><span class="sxs-lookup"><span data-stu-id="15853-697">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="15853-698">Szablon trasy:</span><span class="sxs-lookup"><span data-stu-id="15853-698">The route template:</span></span>

* <span data-ttu-id="15853-699">`{controller=Home}`definiuje `Home` jako domyślne`controller`</span><span class="sxs-lookup"><span data-stu-id="15853-699">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="15853-700">`{action=Index}`definiuje `Index` jako domyślne`action`</span><span class="sxs-lookup"><span data-stu-id="15853-700">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="15853-701">`{id?}`definiuje `id` jako opcjonalne</span><span class="sxs-lookup"><span data-stu-id="15853-701">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="15853-702">Domyślne i opcjonalne parametry trasy nie muszą być obecne w ścieżce URL dla dopasowania.</span><span class="sxs-lookup"><span data-stu-id="15853-702">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="15853-703">Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="15853-703">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="15853-704">`"{controller=Home}/{action=Index}/{id?}"`może być zgodna ze ścieżką URL `/` i będzie generować wartości trasy `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="15853-704">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="15853-705">Wartości dla `controller` i `action` używają wartości domyślnych, `id` nie tworzy wartości, ponieważ w ścieżce URL nie ma odpowiedniego segmentu.</span><span class="sxs-lookup"><span data-stu-id="15853-705">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="15853-706">Aby można było wybrać akcję i, MVC będzie używać tych wartości tras `HomeController` `Index` :</span><span class="sxs-lookup"><span data-stu-id="15853-706">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="15853-707">Przy użyciu tej definicji kontrolera i szablonu trasy `HomeController.Index` Akcja będzie wykonywana dla dowolnej z następujących ścieżek URL:</span><span class="sxs-lookup"><span data-stu-id="15853-707">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="15853-708">Wygodna metoda `UseMvcWithDefaultRoute` :</span><span class="sxs-lookup"><span data-stu-id="15853-708">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="15853-709">Może służyć do zastępowania:</span><span class="sxs-lookup"><span data-stu-id="15853-709">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="15853-710">`UseMvc`i `UseMvcWithDefaultRoute` Dodaj wystąpienie `RouterMiddleware` do potoku programu pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="15853-710">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="15853-711">MVC nie działa bezpośrednio w oprogramowaniu pośredniczącym i używa routingu do obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="15853-711">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="15853-712">MVC jest połączony z trasami za pomocą wystąpienia `MvcRouteHandler` .</span><span class="sxs-lookup"><span data-stu-id="15853-712">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="15853-713">Kod w programie `UseMvc` jest podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="15853-713">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="15853-714">`UseMvc`nie definiuje bezpośrednio żadnych tras, dodaje symbol zastępczy do kolekcji tras dla `attribute` trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-714">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="15853-715">Przeciążenie `UseMvc(Action<IRouteBuilder>)` umożliwia dodanie własnych tras, a także obsługuje routing atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-715">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="15853-716">`UseMvc`i wszystkie jego odmiany dodają symbol zastępczy dla atrybutu trasy — atrybut jest zawsze dostępny niezależnie od sposobu konfigurowania `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="15853-716">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="15853-717">`UseMvcWithDefaultRoute`Definiuje domyślną trasę i obsługuje routing atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-717">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="15853-718">Sekcja [Routing atrybutów](#attribute-routing-ref-label) zawiera więcej szczegółów dotyczących routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-718">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="15853-719">Routing konwencjonalny</span><span class="sxs-lookup"><span data-stu-id="15853-719">Conventional routing</span></span>

<span data-ttu-id="15853-720">`default`Trasa:</span><span class="sxs-lookup"><span data-stu-id="15853-720">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="15853-721">Poprzedni kod jest przykładem konwencjonalnego routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-721">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="15853-722">Ten styl jest nazywany routingiem konwencjonalnym, ponieważ ustanawia *Konwencję* dla ścieżek adresów URL:</span><span class="sxs-lookup"><span data-stu-id="15853-722">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="15853-723">Pierwszy segment ścieżki jest mapowany na nazwę kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-723">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="15853-724">Druga mapowanie na nazwę akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-724">The second maps to the action name.</span></span>
* <span data-ttu-id="15853-725">Trzeci segment jest używany opcjonalnie `id` .</span><span class="sxs-lookup"><span data-stu-id="15853-725">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="15853-726">`id`mapuje do jednostki modelu.</span><span class="sxs-lookup"><span data-stu-id="15853-726">`id` maps to a model entity.</span></span>

<span data-ttu-id="15853-727">Przy użyciu tej `default` trasy ścieżka URL jest `/Products/List` mapowana na `ProductsController.List` akcję i jest `/Blog/Article/17` mapowana na `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="15853-727">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="15853-728">To mapowanie jest oparte **tylko** na nazwach kontrolera i akcji, a nie na podstawie przestrzeni nazw, lokalizacji plików źródłowych ani parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="15853-728">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="15853-729">Użycie konwencjonalnego routingu z domyślną trasą umożliwia szybkie Kompilowanie aplikacji bez konieczności podawania nowego wzorca adresu URL dla każdej zdefiniowanej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-729">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="15853-730">W przypadku aplikacji z akcjami w stylu CRUD zachowanie spójności adresów URL na kontrolerach może pomóc uprościć kod i uczynić interfejs użytkownika bardziej przewidywalny.</span><span class="sxs-lookup"><span data-stu-id="15853-730">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="15853-731">`id`Jest zdefiniowany jako opcjonalny przez szablon trasy, co oznacza, że akcje mogą być wykonywane bez identyfikatora dostarczonego jako część adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-731">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="15853-732">Typowo, co się stanie, jeśli zostanie `id` pominięty z adresu URL, to zostanie ono ustawione na `0` podstawie powiązania modelu, a w rezultacie nie zostanie znaleziona żadna jednostka w dopasowaniu do bazy danych `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="15853-732">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="15853-733">Routing atrybutu może dać szczegółowy formant, który ma być wymagany dla niektórych akcji, a nie dla innych.</span><span class="sxs-lookup"><span data-stu-id="15853-733">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="15853-734">Zgodnie z Konwencją dokumentacja będzie zawierać opcjonalne parametry, takie jak, `id` gdy prawdopodobnie będą widoczne w prawidłowym użyciu.</span><span class="sxs-lookup"><span data-stu-id="15853-734">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="15853-735">Wiele tras</span><span class="sxs-lookup"><span data-stu-id="15853-735">Multiple routes</span></span>

<span data-ttu-id="15853-736">Można dodać wiele tras wewnątrz `UseMvc` , dodając więcej wywołań do `MapRoute` .</span><span class="sxs-lookup"><span data-stu-id="15853-736">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="15853-737">Dzięki temu można zdefiniować wiele Konwencji lub dodać trasy konwencjonalne, które są przeznaczone dla konkretnej akcji, na przykład:</span><span class="sxs-lookup"><span data-stu-id="15853-737">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="15853-738">`blog`Trasa w tym miejscu jest *dedykowaną tradycyjną trasą*, co oznacza, że używa systemu routingu konwencjonalnego, ale jest przeznaczona dla konkretnej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-738">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="15853-739">Ponieważ `controller` i `action` nie pojawiają się w szablonie trasy jako parametry, mogą mieć tylko wartości domyślne i w związku z tym trasa będzie zawsze mapowana na akcję `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="15853-739">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="15853-740">Trasy w kolekcji tras są uporządkowane i będą przetwarzane w kolejności, w jakiej zostały dodane.</span><span class="sxs-lookup"><span data-stu-id="15853-740">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="15853-741">W tym przykładzie `blog` zostanie podjęta próba trasy przed `default` trasą.</span><span class="sxs-lookup"><span data-stu-id="15853-741">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="15853-742">*Dedykowane konwencjonalne trasy* często korzystają z parametrów trasy **catch-all** , takich jak `{*article}` przechwytywanie pozostałej części ścieżki URL.</span><span class="sxs-lookup"><span data-stu-id="15853-742">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="15853-743">Może to spowodować, że trasa "zbyt zachłanne" oznacza, że pasuje do adresów URL, które mają być dopasowane przez inne trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-743">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="15853-744">Umieść trasy "zachłanne" później w tabeli tras, aby rozwiązać ten problem.</span><span class="sxs-lookup"><span data-stu-id="15853-744">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="15853-745">Opcja rezerwowa</span><span class="sxs-lookup"><span data-stu-id="15853-745">Fallback</span></span>

<span data-ttu-id="15853-746">W ramach przetwarzania żądań MVC sprawdzi, czy wartości trasy mogą być używane do znajdowania kontrolera i akcji w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-746">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="15853-747">Jeśli wartości trasy nie są zgodne z akcją, trasa nie jest uważana za dopasowanie i zostanie podjęta kolejna trasa.</span><span class="sxs-lookup"><span data-stu-id="15853-747">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="15853-748">Jest to nazywane *Fallback*i ma na celu uproszczenie przypadków, w których trasy konwencjonalne nakładają się na siebie.</span><span class="sxs-lookup"><span data-stu-id="15853-748">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="15853-749">Niejednoznaczne akcje</span><span class="sxs-lookup"><span data-stu-id="15853-749">Disambiguating actions</span></span>

<span data-ttu-id="15853-750">Gdy dwie akcje są zgodne z routingiem, MVC musi odróżnić się, aby wybrać najlepszy kandydat lub w przeciwnym razie zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="15853-750">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="15853-751">Przykład:</span><span class="sxs-lookup"><span data-stu-id="15853-751">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="15853-752">Ten kontroler definiuje dwie akcje, które byłyby zgodne ze ścieżką URL `/Products/Edit/17` i danymi tras `{ controller = Products, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="15853-752">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="15853-753">Jest to typowy wzorzec dla kontrolerów MVC, gdzie `Edit(int)` pokazuje formularz służący do edytowania produktu i `Edit(int, Product)` przetwarza opublikowany formularz.</span><span class="sxs-lookup"><span data-stu-id="15853-753">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="15853-754">Aby ten możliwy składnik MVC musiał wybrać, `Edit(int, Product)` gdy żądanie jest http `POST` i `Edit(int)` gdy zlecenie http jest inne.</span><span class="sxs-lookup"><span data-stu-id="15853-754">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="15853-755">`HttpPostAttribute`( `[HttpPost]` ) Jest implementacją programu `IActionConstraint` , która będzie zezwalać na wybór akcji tylko wtedy, gdy czasownik http to `POST` .</span><span class="sxs-lookup"><span data-stu-id="15853-755">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="15853-756">Obecność elementu sprawia, że `IActionConstraint` `Edit(int, Product)` lepszym rozwiązaniem jest dopasowanie `Edit(int)` , więc `Edit(int, Product)` zostanie ono najpierw ponowione.</span><span class="sxs-lookup"><span data-stu-id="15853-756">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="15853-757">Musisz tylko pisać `IActionConstraint` implementacje niestandardowe w wyspecjalizowanych scenariuszach, ale ważne jest, aby zrozumieć rolę atrybutów, takich jak `HttpPostAttribute` atrybuty podobne do innych czasowników HTTP.</span><span class="sxs-lookup"><span data-stu-id="15853-757">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="15853-758">W przypadku routingu konwencjonalnego typowe dla akcji używanie tej samej nazwy akcji, gdy są one częścią `show form -> submit form` przepływu pracy.</span><span class="sxs-lookup"><span data-stu-id="15853-758">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="15853-759">Wygoda tego wzorca stanie się bardziej oczywista po przejrzeniu sekcji [zrozumienie IActionConstraint](#understanding-iactionconstraint) .</span><span class="sxs-lookup"><span data-stu-id="15853-759">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="15853-760">Jeśli wiele pasujących tras i MVC nie mogą znaleźć "najlepszej" trasy, spowoduje to zgłoszenie `AmbiguousActionException` .</span><span class="sxs-lookup"><span data-stu-id="15853-760">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="15853-761">Nazwy tras</span><span class="sxs-lookup"><span data-stu-id="15853-761">Route names</span></span>

<span data-ttu-id="15853-762">Ciągi `"blog"` i `"default"` w poniższych przykładach są nazwami tras:</span><span class="sxs-lookup"><span data-stu-id="15853-762">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="15853-763">Nazwy tras nadaj logicznej nazwie trasy, aby nazwana trasa mogła być używana do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-763">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="15853-764">Znacznie upraszcza to tworzenie adresów URL, gdy porządkowanie tras może spowodować skomplikowane generowanie adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-764">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="15853-765">Nazwy tras muszą być unikatowe w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-765">Route names must be unique application-wide.</span></span>

<span data-ttu-id="15853-766">Nazwy tras nie mają wpływu na Dopasowywanie adresów URL ani obsługę żądań; są one używane tylko do generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-766">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="15853-767">[Routing](xref:fundamentals/routing) zawiera bardziej szczegółowe informacje na temat generowania adresów URL, w tym generowanie adresów URL w pomocnikach specyficznych dla MVC.</span><span class="sxs-lookup"><span data-stu-id="15853-767">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="15853-768">Routing atrybutów</span><span class="sxs-lookup"><span data-stu-id="15853-768">Attribute routing</span></span>

<span data-ttu-id="15853-769">Funkcja routingu atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="15853-769">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="15853-770">W poniższym przykładzie `app.UseMvc();` jest używany w `Configure` metodzie i nie jest przesyłana żadna trasa.</span><span class="sxs-lookup"><span data-stu-id="15853-770">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="15853-771">`HomeController`Będzie zgodna z zestawem adresów URL podobnym do trasy domyślnej, która `{controller=Home}/{action=Index}/{id?}` byłaby zgodna:</span><span class="sxs-lookup"><span data-stu-id="15853-771">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="15853-772">`HomeController.Index()`Akcja będzie wykonywana dla dowolnej ścieżki URL `/` , `/Home` lub `/Home/Index` .</span><span class="sxs-lookup"><span data-stu-id="15853-772">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="15853-773">W tym przykładzie przedstawiono najważniejsze różnice programistyczne między routingiem atrybutów i routingiem konwencjonalnym.</span><span class="sxs-lookup"><span data-stu-id="15853-773">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="15853-774">Routing atrybutów wymaga więcej danych wejściowych w celu określenia trasy; konwencjonalne trasy domyślne obsługuje trasy bardziej zwięzłie.</span><span class="sxs-lookup"><span data-stu-id="15853-774">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="15853-775">Jednak Routing atrybutu zezwala na (i wymaga) precyzyjnej kontroli, które szablony tras mają zastosowanie do poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-775">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="15853-776">W przypadku routingu atrybutów nazwa kontrolera i nazwy akcji nie odgrywają **żadnej** roli, w której akcja jest zaznaczona.</span><span class="sxs-lookup"><span data-stu-id="15853-776">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="15853-777">Ten przykład będzie pasował do tych samych adresów URL, co w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="15853-777">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="15853-778">Powyższe szablony tras nie definiują parametrów trasy dla `action` , `area` i `controller` .</span><span class="sxs-lookup"><span data-stu-id="15853-778">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="15853-779">W rzeczywistości te parametry tras są niedozwolone w trasach atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-779">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="15853-780">Ponieważ szablon trasy jest już skojarzony z akcją, nie ma sensu analizy nazwy akcji na podstawie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-780">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="15853-781">Routing atrybutów z atrybutami http [Verb]</span><span class="sxs-lookup"><span data-stu-id="15853-781">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="15853-782">Routing atrybutu może również używać `Http[Verb]` atrybutów, takich jak `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="15853-782">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="15853-783">Wszystkie te atrybuty mogą akceptować szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-783">All of these attributes can accept a route template.</span></span> <span data-ttu-id="15853-784">Ten przykład przedstawia dwie akcje, które pasują do tego samego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="15853-784">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="15853-785">W przypadku ścieżki URL podobnej do `/products` `ProductsApi.ListProducts` akcji zostanie wykonane, gdy zlecenie http jest `GET` i `ProductsApi.CreateProduct` zostanie wykonane, gdy zlecenie http ma wartość `POST` .</span><span class="sxs-lookup"><span data-stu-id="15853-785">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="15853-786">Routing atrybutu najpierw jest zgodny z adresem URL względem zestawu szablonów tras zdefiniowanych przez atrybuty trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-786">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="15853-787">Po dopasowaniu szablonu trasy `IActionConstraint` ograniczenia są stosowane w celu określenia, które akcje mogą być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="15853-787">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="15853-788">Podczas kompilowania interfejsu API REST jest to rzadki, że będzie on używany `[Route(...)]` na potrzeby metody akcji, ponieważ akcja akceptuje wszystkie metody http.</span><span class="sxs-lookup"><span data-stu-id="15853-788">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="15853-789">Lepiej jest użyć bardziej szczegółowych `Http*Verb*Attributes` informacji na temat tego, co obsługuje interfejs API.</span><span class="sxs-lookup"><span data-stu-id="15853-789">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="15853-790">Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i czasowniki HTTP mapują na określone operacje logiczne.</span><span class="sxs-lookup"><span data-stu-id="15853-790">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="15853-791">Ponieważ atrybut Route ma zastosowanie do określonej akcji, można łatwo wprowadzić parametry wymagane jako część definicji szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-791">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="15853-792">W tym przykładzie `id` jest wymagane jako część ścieżki URL.</span><span class="sxs-lookup"><span data-stu-id="15853-792">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="15853-793">`ProductsApi.GetProduct(int)`Akcja zostanie wykonana dla ścieżki URL, na przykład, `/products/3` ale nie dla ścieżki adresu URL `/products` .</span><span class="sxs-lookup"><span data-stu-id="15853-793">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="15853-794">Aby uzyskać pełny opis szablonów tras i powiązanych opcji, zobacz [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="15853-794">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="15853-795">Nazwa trasy</span><span class="sxs-lookup"><span data-stu-id="15853-795">Route Name</span></span>

<span data-ttu-id="15853-796">Następujący kod definiuje *nazwę trasy* `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="15853-796">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="15853-797">Nazwy tras mogą służyć do generowania adresów URL na podstawie określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-797">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="15853-798">Nazwy tras nie mają wpływu na zachowanie routingu w adresie URL i są używane tylko na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-798">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="15853-799">Nazwy tras muszą być unikatowe w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-799">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="15853-800">Jest to zróżnicowane dla konwencjonalnej *trasy domyślnej*, która definiuje `id` parametr jako opcjonalny ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="15853-800">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="15853-801">Ta możliwość precyzyjnego określania interfejsów API ma zalety, takich jak umożliwienie `/products` i `/products/5` wysyłanie ich do różnych akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-801">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="15853-802">Łączenie tras</span><span class="sxs-lookup"><span data-stu-id="15853-802">Combining routes</span></span>

<span data-ttu-id="15853-803">Aby mniej powtarzać Routing atrybutów, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-803">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="15853-804">Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzone w celu rozesłania szablonów w akcjach.</span><span class="sxs-lookup"><span data-stu-id="15853-804">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="15853-805">Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w kontrolerze używają routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-805">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="15853-806">W tym przykładzie ścieżka adresu URL `/products` może być taka sama `ProductsApi.ListProducts` , a Ścieżka adresu URL `/products/5` może być taka sama `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="15853-806">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="15853-807">Obie te akcje pasują tylko do protokołu HTTP `GET` , ponieważ są oznaczone za pomocą `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="15853-807">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="15853-808">Szablony tras zastosowane do akcji rozpoczynającej się od `/` lub `~/` nie są połączone z szablonami tras zastosowanymi do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-808">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="15853-809">Ten przykład dopasowuje zestaw ścieżek URL podobny do *trasy domyślnej*.</span><span class="sxs-lookup"><span data-stu-id="15853-809">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="15853-810">Określanie kolejności tras atrybutów</span><span class="sxs-lookup"><span data-stu-id="15853-810">Ordering attribute routes</span></span>

<span data-ttu-id="15853-811">W przeciwieństwie do konwencjonalnych tras, które są wykonywane w określonej kolejności, routing atrybutu kompiluje drzewo i dopasowuje wszystkie trasy jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="15853-811">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="15853-812">Zachowuje się tak, jakby wpisy trasy zostały umieszczone w idealnym porządku; najbardziej konkretne trasy mają możliwość wykonania przed bardziej ogólnymi trasami.</span><span class="sxs-lookup"><span data-stu-id="15853-812">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="15853-813">Na przykład taka trasa `blog/search/{topic}` jest bardziej szczegółowa niż trasa, taka jak `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="15853-813">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="15853-814">Logicznie mówiąc, `blog/search/{topic}` że trasy "uruchomienia" są najpierw domyślnie, ponieważ to jest jedyną rozsądną kolejnością.</span><span class="sxs-lookup"><span data-stu-id="15853-814">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="15853-815">Przy użyciu konwencjonalnego routingu deweloper jest odpowiedzialny za umieszczanie tras w odpowiedniej kolejności.</span><span class="sxs-lookup"><span data-stu-id="15853-815">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="15853-816">Trasy atrybutów mogą konfigurować kolejność przy użyciu `Order` właściwości wszystkich atrybutów tras dostarczonych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="15853-816">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="15853-817">Trasy są przetwarzane zgodnie z rosnącą sortowaniem `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="15853-817">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="15853-818">Kolejność domyślna to `0` .</span><span class="sxs-lookup"><span data-stu-id="15853-818">The default order is `0`.</span></span> <span data-ttu-id="15853-819">Ustawienie trasy przy użyciu `Order = -1` zostanie uruchomione przed trasami, które nie ustawiają kolejności.</span><span class="sxs-lookup"><span data-stu-id="15853-819">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="15853-820">Ustawienie trasy przy użyciu `Order = 1` zostanie uruchomione po określeniu domyślnej kolejności tras.</span><span class="sxs-lookup"><span data-stu-id="15853-820">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="15853-821">Należy unikać w zależności od `Order` .</span><span class="sxs-lookup"><span data-stu-id="15853-821">Avoid depending on `Order`.</span></span> <span data-ttu-id="15853-822">Jeśli przestrzeń adresów URL wymaga jawnych wartości kolejności, aby można było prawidłowo kierować trasy, to prawdopodobnie również jest myląca dla klientów.</span><span class="sxs-lookup"><span data-stu-id="15853-822">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="15853-823">W ogólnym routingu atrybutów wybierz prawidłową trasę z dopasowywaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-823">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="15853-824">Jeśli domyślna kolejność generowania adresów URL nie działa, użycie nazwy trasy jako przesłonięcia jest zwykle prostsze niż stosowanie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="15853-824">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="15853-825">Strony routingu i routingu kontrolera MVC współdzielą implementację.</span><span class="sxs-lookup"><span data-stu-id="15853-825"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="15853-826">Informacje o zamówieniu trasy w Razor tematach stron są dostępne na [ Razor stronach trasy i konwencje aplikacji: kolejność trasy](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="15853-826">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="15853-827">Zastępowanie tokenu w szablonach tras ([Controller], [Action], [obszar])</span><span class="sxs-lookup"><span data-stu-id="15853-827">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="15853-828">Dla wygody, trasy atrybutu obsługują *Zastępowanie tokenu* przez ujęcie tokenu w nawiasy kwadratowe ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="15853-828">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="15853-829">Tokeny `[action]` , `[area]` i `[controller]` są zastępowane wartościami nazwy akcji, obszaru i nazwy kontrolera z akcji, w której jest zdefiniowana trasa.</span><span class="sxs-lookup"><span data-stu-id="15853-829">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="15853-830">W poniższym przykładzie akcje są zgodne ze ścieżkami URL zgodnie z opisem w komentarzach:</span><span class="sxs-lookup"><span data-stu-id="15853-830">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="15853-831">Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-831">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="15853-832">Powyższy przykład będzie zachowywać się tak samo jak poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="15853-832">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="15853-833">Trasy atrybutu można także łączyć z dziedziczeniem.</span><span class="sxs-lookup"><span data-stu-id="15853-833">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="15853-834">Jest to szczególnie zaawansowane w połączeniu z zastępowaniem tokenu.</span><span class="sxs-lookup"><span data-stu-id="15853-834">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="15853-835">Zastępowanie tokenu dotyczy również nazw tras zdefiniowanych przez trasy atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-835">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="15853-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="15853-837">Aby dopasować ogranicznik zamiany tokenu literału `[` lub `]` , należy to zrobić, powtarzając znak ( `[[` lub `]]` ).</span><span class="sxs-lookup"><span data-stu-id="15853-837">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="15853-838">Używanie transformatora parametrów do dostosowywania zastępowania tokenu</span><span class="sxs-lookup"><span data-stu-id="15853-838">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="15853-839">Zastępowanie tokenu można dostosować za pomocą transformatora parametrów.</span><span class="sxs-lookup"><span data-stu-id="15853-839">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="15853-840">Transformator parametrów implementuje `IOutboundParameterTransformer` i przekształca wartość parametrów.</span><span class="sxs-lookup"><span data-stu-id="15853-840">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="15853-841">Na przykład niestandardowy `SlugifyParameterTransformer` transformator parametrów zmienia `SubscriptionManagement` wartość trasy na `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="15853-841">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="15853-842">`RouteTokenTransformerConvention`Jest konwencją modelu aplikacji, która:</span><span class="sxs-lookup"><span data-stu-id="15853-842">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="15853-843">Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-843">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="15853-844">Dostosowuje wartości tokenów tras w miarę ich wymiany.</span><span class="sxs-lookup"><span data-stu-id="15853-844">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="15853-845">`RouteTokenTransformerConvention`Jest zarejestrowany jako opcja w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="15853-845">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="15853-846">Wiele tras</span><span class="sxs-lookup"><span data-stu-id="15853-846">Multiple Routes</span></span>

<span data-ttu-id="15853-847">Routing atrybutów obsługuje definiowanie wielu tras, które docierają do tej samej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-847">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="15853-848">Najczęstszym sposobem użycia tej metody jest naśladowanie zachowania *domyślnej trasy konwencjonalnej* , jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="15853-848">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="15853-849">Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każda z nich będzie łączyć się z poszczególnymi atrybutami trasy w metodach akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-849">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="15853-850">Gdy wiele atrybutów trasy (które Implementuj `IActionConstraint` ) jest umieszczanych w akcji, każde ograniczenie akcji łączy się z szablonem trasy z atrybutu, który go zdefiniował.</span><span class="sxs-lookup"><span data-stu-id="15853-850">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="15853-851">Chociaż używanie wielu tras w akcjach może wydawać się zaawansowane, lepiej jest zachować prosty i dobrze zdefiniowany obszar adresów URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-851">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="15853-852">Użyj wielu tras w przypadku akcji tylko wtedy, gdy jest to konieczne, na przykład do obsługi istniejących klientów.</span><span class="sxs-lookup"><span data-stu-id="15853-852">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="15853-853">Określanie opcjonalnych parametrów trasy, wartości domyślnych i ograniczeń</span><span class="sxs-lookup"><span data-stu-id="15853-853">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="15853-854">Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="15853-854">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="15853-855">Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="15853-855">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="15853-856">Niestandardowe atrybuty trasy przy użyciu`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="15853-856">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="15853-857">Wszystkie atrybuty trasy podane w strukturze ( `[Route(...)]` , `[HttpGet(...)]` itp.) implementują `IRouteTemplateProvider` interfejs.</span><span class="sxs-lookup"><span data-stu-id="15853-857">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="15853-858">MVC wyszukuje atrybuty klas kontrolera i metod akcji, gdy aplikacja jest uruchamiana i używa tych, które implementują w `IRouteTemplateProvider` celu utworzenia początkowego zestawu tras.</span><span class="sxs-lookup"><span data-stu-id="15853-858">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="15853-859">Można zaimplementować, `IRouteTemplateProvider` Aby zdefiniować własne atrybuty trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-859">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="15853-860">Każdy `IRouteTemplateProvider` z nich umożliwia definiowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:</span><span class="sxs-lookup"><span data-stu-id="15853-860">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="15853-861">Atrybut z powyższego przykładu automatycznie ustawia wartość `Template` na `"api/[controller]"` gdy `[MyApiController]` jest stosowany.</span><span class="sxs-lookup"><span data-stu-id="15853-861">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="15853-862">Dostosowywanie tras atrybutów przy użyciu modelu aplikacji</span><span class="sxs-lookup"><span data-stu-id="15853-862">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="15853-863">*Model aplikacji* jest modelem obiektów tworzonym podczas uruchamiania ze wszystkimi metadanymi używanymi przez MVC do kierowania i wykonywania akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-863">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="15853-864">*Model aplikacji* zawiera wszystkie dane zebrane z atrybutów trasy (do `IRouteTemplateProvider` ).</span><span class="sxs-lookup"><span data-stu-id="15853-864">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="15853-865">Można napisać *konwencje* , aby zmodyfikować model aplikacji w czasie uruchamiania, aby dostosować sposób zachowania routingu.</span><span class="sxs-lookup"><span data-stu-id="15853-865">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="15853-866">W tej sekcji przedstawiono prosty przykład dostosowywania routingu przy użyciu modelu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15853-866">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="15853-867">Routing mieszany: Routing atrybutów a konwencjonalny Routing</span><span class="sxs-lookup"><span data-stu-id="15853-867">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="15853-868">Aplikacje MVC mogą łączyć użycie konwencjonalnego routingu i routingu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="15853-868">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="15853-869">Typowym zastosowaniem są trasy konwencjonalne dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.</span><span class="sxs-lookup"><span data-stu-id="15853-869">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="15853-870">Akcje są albo podkierowane do Konwencji lub kierowane przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="15853-870">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="15853-871">Umieszczenie trasy na kontrolerze lub akcja powoduje, że atrybut jest kierowany.</span><span class="sxs-lookup"><span data-stu-id="15853-871">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="15853-872">Akcje definiujące trasy atrybutów nie mogą być osiągane za pomocą konwencjonalnych tras i vice versa.</span><span class="sxs-lookup"><span data-stu-id="15853-872">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="15853-873">**Każdy** atrybut trasy na kontrolerze powoduje kierowanie wszystkich akcji w atrybucie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="15853-873">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="15853-874">Co odróżnia dwa typy systemów routingu, proces stosowany po adresie URL pasuje do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-874">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="15853-875">W przypadku routingu konwencjonalnego wartości trasy z dopasowania są używane do wybierania akcji i kontrolera z tabeli odnośników wszystkich konwencjonalnych akcji kierowanych.</span><span class="sxs-lookup"><span data-stu-id="15853-875">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="15853-876">W obszarze Routing atrybutów każdy szablon jest już skojarzony z akcją i nie jest wymagany dalsze wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="15853-876">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="15853-877">Złożone segmenty</span><span class="sxs-lookup"><span data-stu-id="15853-877">Complex segments</span></span>

<span data-ttu-id="15853-878">Złożone segmenty (na przykład `[Route("/dog{token}cat")]` ) są przetwarzane przez dopasowanie literałów z prawej strony do lewej w sposób niezachłanney.</span><span class="sxs-lookup"><span data-stu-id="15853-878">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="15853-879">Sprawdź [kod źródłowy](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) opisu.</span><span class="sxs-lookup"><span data-stu-id="15853-879">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="15853-880">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="15853-880">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="15853-881">Generowanie adresu URL</span><span class="sxs-lookup"><span data-stu-id="15853-881">URL Generation</span></span>

<span data-ttu-id="15853-882">Aplikacje MVC mogą używać funkcji generowania adresów URL routingu do generowania linków URL do akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-882">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="15853-883">Generowanie adresów URL eliminuje adresy URL zakodowana, co sprawia, że kod jest bardziej niezawodny i konserwowany.</span><span class="sxs-lookup"><span data-stu-id="15853-883">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="15853-884">Ta sekcja koncentruje się na funkcjach generowania adresów URL dostarczonych przez MVC i obejmuje tylko podstawowe informacje na temat sposobu działania generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-884">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="15853-885">Aby uzyskać szczegółowy opis generowania adresów URL, zobacz temat [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="15853-885">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="15853-886">`IUrlHelper`Interfejs jest podstawową częścią infrastruktury między MVC i routingiem na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-886">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="15853-887">Wystąpienie `IUrlHelper` dostępne za pomocą właściwości można znaleźć `Url` w obszarze Kontrolery, widoki i składniki widoku.</span><span class="sxs-lookup"><span data-stu-id="15853-887">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="15853-888">W tym przykładzie `IUrlHelper` interfejs jest używany przez `Controller.Url` Właściwość w celu WYGENEROWANIA adresu URL dla innej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-888">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="15853-889">Jeśli aplikacja używa domyślnej trasy konwencjonalnej, wartość `url` zmiennej będzie ciąg ścieżki URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="15853-889">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="15853-890">Ta ścieżka URL jest tworzona przez Routing przez połączenie wartości trasy z bieżącego żądania (wartości otoczenia), z wartościami przekazanymi do `Url.Action` i podstawianie tych wartości do szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="15853-890">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="15853-891">Każdy parametr trasy w szablonie trasy ma swoją wartość zastępowaną przez pasujące nazwy wartościami i wartościami otoczenia.</span><span class="sxs-lookup"><span data-stu-id="15853-891">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="15853-892">Parametr trasy, który nie ma wartości, może korzystać z wartości domyślnej, jeśli ma taką wartość, lub być pominięty, jeśli jest opcjonalny (tak jak w przypadku `id` w przypadku w tym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="15853-892">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="15853-893">Generowanie adresu URL zakończy się niepowodzeniem, jeśli którykolwiek z wymaganych parametrów trasy nie ma odpowiadającej wartości.</span><span class="sxs-lookup"><span data-stu-id="15853-893">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="15853-894">Jeśli generowanie adresów URL kończy się niepowodzeniem dla trasy, kolejna trasa zostanie ponowiona do momentu przetworzenia wszystkich tras lub znalezienia dopasowania.</span><span class="sxs-lookup"><span data-stu-id="15853-894">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="15853-895">W `Url.Action` powyższym przykładzie przyjęto założenie konwencjonalnego routingu, ale generowanie adresów URL działa podobnie jak w przypadku routingu atrybutów, chociaż koncepcje różnią się.</span><span class="sxs-lookup"><span data-stu-id="15853-895">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="15853-896">W przypadku routingu konwencjonalnego wartości trasy służą do rozszerzania szablonu, a wartości trasy dla `controller` i `action` zwykle pojawiają się w tym szablonie — działa to, ponieważ adresy URL dopasowane przez Routing są zgodne z *Konwencją*.</span><span class="sxs-lookup"><span data-stu-id="15853-896">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="15853-897">W obszarze Routing atrybutów wartości trasy dla `controller` i `action` nie mogą występować w szablonie — zamiast tego są używane do wyszukiwania szablonu do użycia.</span><span class="sxs-lookup"><span data-stu-id="15853-897">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="15853-898">W tym przykładzie zastosowano Routing atrybutów:</span><span class="sxs-lookup"><span data-stu-id="15853-898">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="15853-899">MVC kompiluje tabelę odnośników wszystkich akcji przypisanych do atrybutu i odpowiada `controller` wartości i, `action` Aby wybrać szablon trasy do użycia na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-899">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="15853-900">W powyższym przykładzie `custom/url/to/destination` jest generowany.</span><span class="sxs-lookup"><span data-stu-id="15853-900">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="15853-901">Generowanie adresów URL według nazwy akcji</span><span class="sxs-lookup"><span data-stu-id="15853-901">Generating URLs by action name</span></span>

<span data-ttu-id="15853-902">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="15853-902">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="15853-903">`Action`) i wszystkie powiązane przeciążenia są oparte na tym pomysłie, że chcesz określić, do czego łączysz, określając nazwę kontrolera i nazwę akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-903">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="15853-904">W przypadku używania `Url.Action` , bieżąca wartość trasy dla `controller` i `action` jest określona dla Ciebie — wartości `controller` i `action` są częścią obu *wartości otoczenia* **i** *wartości*.</span><span class="sxs-lookup"><span data-stu-id="15853-904">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="15853-905">Metoda `Url.Action` zawsze używa bieżących wartości `action` i `controller` i wygeneruje ścieżkę URL, która jest przesyłana do bieżącej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-905">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="15853-906">Funkcja routingu próbuje użyć wartości w otoczeniu wartości, aby podać informacje, które nie zostały wprowadzone podczas generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-906">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="15853-907">Przy użyciu trasy podobnej do `{a}/{b}/{c}/{d}` i otoczenia `{ a = Alice, b = Bob, c = Carol, d = David }` Routing ma wystarczającą ilość informacji do WYGENEROWANIA adresu URL bez żadnych dodatkowych wartości — ponieważ wszystkie parametry trasy mają wartość.</span><span class="sxs-lookup"><span data-stu-id="15853-907">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="15853-908">Po dodaniu wartości wartość zostanie `{ d = Donovan }` `{ d = David }` zignorowana, a wygenerowana Ścieżka adresu URL `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="15853-908">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="15853-909">Ścieżki URL są hierarchiczne.</span><span class="sxs-lookup"><span data-stu-id="15853-909">URL paths are hierarchical.</span></span> <span data-ttu-id="15853-910">W powyższym przykładzie, jeśli dodano wartość `{ c = Cheryl }` , obie wartości `{ c = Carol, d = David }` byłyby ignorowane.</span><span class="sxs-lookup"><span data-stu-id="15853-910">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="15853-911">W takim przypadku nie jest już dostępna wartość `d` i generowanie adresu URL zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="15853-911">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="15853-912">Należy określić pożądaną wartość `c` i `d` .</span><span class="sxs-lookup"><span data-stu-id="15853-912">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="15853-913">Można oczekiwać, że ten problem zostanie osiągnięty przy użyciu trasy domyślnej ( `{controller}/{action}/{id?}` ) — ale w takiej sytuacji rzadko napotkasz to zachowanie, ponieważ `Url.Action` zawsze jawnie określimy `controller` `action` wartość i.</span><span class="sxs-lookup"><span data-stu-id="15853-913">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="15853-914">Dłuższe przeciążenia `Url.Action` również pobierają dodatkowy obiekt *wartości trasy* , aby zapewnić wartości parametrów trasy innych niż `controller` i `action` .</span><span class="sxs-lookup"><span data-stu-id="15853-914">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="15853-915">Najczęściej zobaczysz, że będzie on używany z usługą `id` `Url.Action("Buy", "Products", new { id = 17 })` .</span><span class="sxs-lookup"><span data-stu-id="15853-915">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="15853-916">Według Konwencji obiekt *wartości trasy* jest zwykle obiektem typu anonimowego, ale może również być `IDictionary<>` lub *zwykłym starym obiektem platformy .NET*.</span><span class="sxs-lookup"><span data-stu-id="15853-916">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="15853-917">Wszystkie dodatkowe wartości trasy, które nie pasują do parametrów trasy, są umieszczane w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="15853-917">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="15853-918">Aby utworzyć bezwzględny adres URL, Użyj przeciążenia, które akceptuje `protocol` :`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="15853-918">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="15853-919">Generowanie adresów URL według trasy</span><span class="sxs-lookup"><span data-stu-id="15853-919">Generating URLs by route</span></span>

<span data-ttu-id="15853-920">Powyższy kod wygeneruje adres URL przez przekazanie go do kontrolera i nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-920">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="15853-921">`IUrlHelper`zapewnia także `Url.RouteUrl` rodzinę metod.</span><span class="sxs-lookup"><span data-stu-id="15853-921">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="15853-922">Te metody są podobne do `Url.Action` , ale nie kopiują bieżących wartości `action` i `controller` do wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-922">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="15853-923">Najbardziej typowym zastosowaniem jest określenie nazwy trasy do użycia określonej trasy do wygenerowania adresu URL, na ogół *bez* określania kontrolera lub nazwy akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-923">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="15853-924">Generowanie adresów URL w kodzie HTML</span><span class="sxs-lookup"><span data-stu-id="15853-924">Generating URLs in HTML</span></span>

<span data-ttu-id="15853-925">`IHtmlHelper`zapewnia `HtmlHelper` metody `Html.BeginForm` i `Html.ActionLink` wygenerowanie `<form>` `<a>` odpowiednio elementów i.</span><span class="sxs-lookup"><span data-stu-id="15853-925">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="15853-926">Metody te używają `Url.Action` metody do generowania adresu URL i akceptują podobne argumenty.</span><span class="sxs-lookup"><span data-stu-id="15853-926">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="15853-927">`Url.RouteUrl`Pomocników dla programu `HtmlHelper` mają `Html.BeginRouteForm` `Html.RouteLink` podobną funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="15853-927">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="15853-928">TagHelpers Generuj adresy URL za pomocą `form` TagHelper i `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="15853-928">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="15853-929">Oba te zastosowania `IUrlHelper` dla ich implementacji.</span><span class="sxs-lookup"><span data-stu-id="15853-929">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="15853-930">Aby uzyskać więcej informacji, zobacz [Praca z formularzami](../views/working-with-forms.md) .</span><span class="sxs-lookup"><span data-stu-id="15853-930">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="15853-931">W widokach `IUrlHelper` można korzystać z `Url` właściwości dla dowolnej generacji adresów URL ad hoc, która nie jest objęta powyższym.</span><span class="sxs-lookup"><span data-stu-id="15853-931">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="15853-932">Generowanie adresów URL w wynikach akcji</span><span class="sxs-lookup"><span data-stu-id="15853-932">Generating URLS in Action Results</span></span>

<span data-ttu-id="15853-933">Powyższe przykłady przedstawiono przy użyciu `IUrlHelper` w kontrolerze, podczas gdy najbardziej typowym użyciem na kontrolerze jest wygenerowanie adresu URL jako części wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-933">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="15853-934">`ControllerBase` `Controller` Klasy bazowe i zapewniają wygodne metody dla wyników akcji, które odwołują się do innej akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-934">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="15853-935">Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="15853-935">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="15853-936">Metody fabryki wyników akcji postępują zgodnie z podobnym wzorcem do metod w `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="15853-936">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="15853-937">Specjalny przypadek dla dedykowanych tras konwencjonalnych</span><span class="sxs-lookup"><span data-stu-id="15853-937">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="15853-938">Funkcja routingu konwencjonalnego może używać specjalnego rodzaju definicji trasy o nazwie *dedykowanej, konwencjonalnej trasy*.</span><span class="sxs-lookup"><span data-stu-id="15853-938">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="15853-939">W poniższym przykładzie trasa o nazwie `blog` jest dedykowaną umowną trasą.</span><span class="sxs-lookup"><span data-stu-id="15853-939">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="15853-940">Użycie tych definicji tras `Url.Action("Index", "Home")` spowoduje wygenerowanie ścieżki URL `/` z `default` trasą, ale dlaczego?</span><span class="sxs-lookup"><span data-stu-id="15853-940">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="15853-941">Możliwe jest odpuszczenie, że wartości trasy `{ controller = Home, action = Index }` byłyby wystarczające do wygenerowania adresu URL przy użyciu `blog` , a wynikiem będzie `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="15853-941">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="15853-942">Dedykowane konwencjonalne trasy polegają na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiadającego parametru trasy, który uniemożliwia trasie "zbyt zachłanne" z generowaniem adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-942">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="15853-943">W takim przypadku wartości domyślne to `{ controller = Blog, action = Article }` , a ani nie `controller` `action` pojawiają się jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-943">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="15853-944">Gdy Routing wykonuje generowanie adresów URL, podane wartości muszą być zgodne z wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="15853-944">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="15853-945">Generowanie adresu URL `blog` nie powiedzie się, ponieważ wartości `{ controller = Home, action = Index }` nie są zgodne `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="15853-945">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="15853-946">Następnie usługa routingu wraca do wypróbowania `default` , która się powiedzie.</span><span class="sxs-lookup"><span data-stu-id="15853-946">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="15853-947">Obszary</span><span class="sxs-lookup"><span data-stu-id="15853-947">Areas</span></span>

<span data-ttu-id="15853-948">[Obszary](areas.md) są funkcją MVC służącą do organizowania powiązanych funkcji w grupie jako oddzielnej przestrzeni nazw routingu (dla akcji kontrolera) i struktury folderów (dla widoków).</span><span class="sxs-lookup"><span data-stu-id="15853-948">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="15853-949">Użycie obszarów umożliwia aplikacji posiadanie wielu kontrolerów o tej samej nazwie, o ile mają one różne *obszary*.</span><span class="sxs-lookup"><span data-stu-id="15853-949">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="15853-950">Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie kolejnego parametru trasy `area` do `controller` i `action` .</span><span class="sxs-lookup"><span data-stu-id="15853-950">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="15853-951">W tej sekcji omówiono sposób, w jaki Routing współdziała z obszarami — zobacz [obszary](areas.md) , aby uzyskać szczegółowe informacje o tym, jak obszary są używane w widokach.</span><span class="sxs-lookup"><span data-stu-id="15853-951">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="15853-952">Poniższy przykład konfiguruje MVC do używania domyślnej trasy konwencjonalnej i *trasy obszaru* dla obszaru o nazwie `Blog` :</span><span class="sxs-lookup"><span data-stu-id="15853-952">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="15853-953">W przypadku dopasowania ścieżki adresu URL `/Manage/Users/AddUser` , na przykład, Pierwsza trasa spowoduje wygenerowanie wartości trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="15853-953">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="15853-954">`area`Wartość trasy jest generowana przez wartość domyślną dla `area` , w rzeczywistości trasa utworzona przez `MapAreaRoute` jest równoważna z następującymi:</span><span class="sxs-lookup"><span data-stu-id="15853-954">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="15853-955">`MapAreaRoute`tworzy trasę przy użyciu wartości domyślnej i ograniczenia dotyczącej `area` użycia podanej nazwy obszaru, w tym przypadku `Blog` .</span><span class="sxs-lookup"><span data-stu-id="15853-955">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="15853-956">Wartość domyślna zapewnia, że trasa zawsze produkuje `{ area = Blog, ... }` , ograniczenie wymaga wartości `{ area = Blog, ... }` dla generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-956">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="15853-957">Routowanie konwencjonalne jest zależne od kolejności.</span><span class="sxs-lookup"><span data-stu-id="15853-957">Conventional routing is order-dependent.</span></span> <span data-ttu-id="15853-958">Ogólnie rzecz biorąc, trasy z obszarami należy umieścić wcześniej w tabeli tras, ponieważ są one bardziej specyficzne niż trasy bez obszaru.</span><span class="sxs-lookup"><span data-stu-id="15853-958">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="15853-959">Korzystając z powyższego przykładu, wartości trasy będą zgodne z następującą akcją:</span><span class="sxs-lookup"><span data-stu-id="15853-959">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="15853-960">`AreaAttribute`Wskazuje to, co oznacza kontroler w ramach obszaru. Załóżmy, że ten kontroler znajduje się w `Blog` obszarze.</span><span class="sxs-lookup"><span data-stu-id="15853-960">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="15853-961">Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru i **nie** będą zgodne, gdy `area` wartość trasy zostanie podana przez Routing.</span><span class="sxs-lookup"><span data-stu-id="15853-961">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="15853-962">W poniższym przykładzie tylko pierwszy kontroler może być zgodny z wartościami trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="15853-962">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="15853-963">Przestrzeń nazw każdego kontrolera jest pokazana w tym miejscu w celu zapewnienia kompletności — w przeciwnym razie kontrolery będą mieć konflikt nazw i generują błąd kompilatora.</span><span class="sxs-lookup"><span data-stu-id="15853-963">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="15853-964">Przestrzenie nazw klas nie mają wpływu na Routing MVC.</span><span class="sxs-lookup"><span data-stu-id="15853-964">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="15853-965">Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko wtedy, gdy ich nazwa obszaru jest podana przez `area` wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-965">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="15853-966">Trzeci kontroler nie jest członkiem żadnego obszaru i można go dopasować tylko wtedy, gdy nie ma żadnej wartości parametru `area` Routing.</span><span class="sxs-lookup"><span data-stu-id="15853-966">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="15853-967">W warunkach pasujących do *nie ma żadnej*wartości, brak `area` wartości jest taka sama jak wartość `area` null lub pusty ciąg.</span><span class="sxs-lookup"><span data-stu-id="15853-967">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="15853-968">Podczas wykonywania akcji wewnątrz obszaru wartość trasy dla `area` będzie dostępna jako *wartość otoczenia* dla routingu do użycia na potrzeby generowania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="15853-968">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="15853-969">Oznacza to, że domyślnie obszary programu *Sticky Notes* mają być używane do generowania adresów URL, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="15853-969">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="15853-970">Zrozumienie IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="15853-970">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="15853-971">Ta sekcja jest głęboką szczegółoweą wewnętrznych struktur oraz jak MVC wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="15853-971">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="15853-972">Typowa aplikacja nie będzie potrzebować niestandardowego`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="15853-972">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="15853-973">Prawdopodobnie już było używane, `IActionConstraint` nawet jeśli nie masz doświadczenia z interfejsem.</span><span class="sxs-lookup"><span data-stu-id="15853-973">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="15853-974">`[HttpGet]`Atrybut i podobne `[Http-VERB]` atrybuty implementują `IActionConstraint` w celu ograniczenia wykonywania metody akcji.</span><span class="sxs-lookup"><span data-stu-id="15853-974">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="15853-975">Przy założeniu domyślnej trasy konwencjonalnej, ścieżka URL `/Products/Edit` będzie generować wartości `{ controller = Products, action = Edit }` , które byłyby zgodne z **obiema** akcjami przedstawionymi w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="15853-975">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="15853-976">W `IActionConstraint` terminologii mamy, że obie te akcje są uważane za kandydatów, ponieważ oba te są zgodne z danymi trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-976">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="15853-977">Gdy `HttpGetAttribute` zostanie wykonane, zobaczy, że *Edit ()* jest dopasowaniem do *Get* i nie jest dopasowaniem dla żadnego innego zlecenia http.</span><span class="sxs-lookup"><span data-stu-id="15853-977">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="15853-978">`Edit(...)`Akcja nie ma zdefiniowanych żadnych ograniczeń i dlatego będzie pasować do dowolnego czasownika http.</span><span class="sxs-lookup"><span data-stu-id="15853-978">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="15853-979">Dlatego przy założeniu, że `POST` `Edit(...)` dopasowań są tylko.</span><span class="sxs-lookup"><span data-stu-id="15853-979">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="15853-980">Jednak w przypadku `GET` obu akcji można nadal dopasować się, jednak akcja z funkcją `IActionConstraint` jest zawsze uznawana za *lepszą* od akcji bez.</span><span class="sxs-lookup"><span data-stu-id="15853-980">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="15853-981">Dlatego, że jest `Edit()` `[HttpGet]` traktowany jako bardziej szczegółowy i zostanie wybrany, jeśli obie akcje mogą być zgodne.</span><span class="sxs-lookup"><span data-stu-id="15853-981">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="15853-982">Koncepcyjnie, `IActionConstraint` jest formą *przeciążenia*, ale zamiast przeciążać metody o tej samej nazwie, przeciążanie między akcjami, które pasują do tego samego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15853-982">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="15853-983">Funkcja routingu atrybutów używa także `IActionConstraint` i może powodować, że działania z różnych kontrolerów są traktowane jako kandydaci.</span><span class="sxs-lookup"><span data-stu-id="15853-983">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="15853-984">Implementowanie IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="15853-984">Implementing IActionConstraint</span></span>

<span data-ttu-id="15853-985">Najprostszym sposobem implementacji `IActionConstraint` jest utworzenie klasy pochodnej od `System.Attribute` i umieszczenie jej w akcjach i kontrolerach.</span><span class="sxs-lookup"><span data-stu-id="15853-985">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="15853-986">MVC automatycznie odnajdzie wszystkie `IActionConstraint` , które są stosowane jako atrybuty.</span><span class="sxs-lookup"><span data-stu-id="15853-986">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="15853-987">Możesz użyć modelu aplikacji, aby zastosować ograniczenia i prawdopodobnie jest to najbardziej elastyczne podejście, ponieważ pozwala to na to, w jaki sposób są stosowane.</span><span class="sxs-lookup"><span data-stu-id="15853-987">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="15853-988">W poniższym przykładzie ograniczenie wybiera akcję na podstawie *kodu kraju* z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="15853-988">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="15853-989">[Pełny przykład w witrynie GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="15853-989">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="15853-990">Użytkownik jest odpowiedzialny za implementację `Accept` metody i wybranie "Order" dla ograniczenia, które ma zostać wykonane.</span><span class="sxs-lookup"><span data-stu-id="15853-990">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="15853-991">W tym przypadku `Accept` Metoda zwraca wartość, `true` aby zauważyć, że akcja jest dopasowanie, gdy `country` wartość trasy jest zgodna.</span><span class="sxs-lookup"><span data-stu-id="15853-991">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="15853-992">Różni się to od elementu `RouteValueAttribute` , który umożliwia powrót do akcji, która nie ma atrybutu.</span><span class="sxs-lookup"><span data-stu-id="15853-992">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="15853-993">Przykład pokazuje, że w przypadku zdefiniowania `en-US` akcji, kod kraju, taki jak, powróci `fr-FR` do bardziej ogólnego kontrolera, który nie został `[CountrySpecific(...)]` zastosowany.</span><span class="sxs-lookup"><span data-stu-id="15853-993">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="15853-994">`Order`Właściwość decyduje, *który etap* jest częścią tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="15853-994">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="15853-995">Ograniczenia akcji są uruchamiane w grupach na podstawie `Order` .</span><span class="sxs-lookup"><span data-stu-id="15853-995">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="15853-996">Na przykład wszystkie atrybuty metody HTTP podane przez platformę używają tej samej `Order` wartości, aby były uruchamiane na tym samym etapie.</span><span class="sxs-lookup"><span data-stu-id="15853-996">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="15853-997">Możesz mieć tyle etapów, ile potrzebujesz, aby zaimplementować odpowiednie zasady.</span><span class="sxs-lookup"><span data-stu-id="15853-997">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="15853-998">Aby zdecydować, `Order` czy należy zastosować ograniczenie, przed metodami http.</span><span class="sxs-lookup"><span data-stu-id="15853-998">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="15853-999">Najpierw należy uruchomić mniejsze numery.</span><span class="sxs-lookup"><span data-stu-id="15853-999">Lower numbers run first.</span></span>

::: moniker-end
