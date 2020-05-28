---
<span data-ttu-id="1c6eb-101">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-102">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-103">'Identity'</span></span>
- <span data-ttu-id="1c6eb-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-105">'Razor'</span></span>
- <span data-ttu-id="1c6eb-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="1c6eb-107">BlazorRouting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c6eb-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="1c6eb-108">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1c6eb-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1c6eb-109">Dowiedz się, jak kierować żądania i jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnika do tworzenia linków nawigacji w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="1c6eb-110">ASP.NET Core integracja z routingiem punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="1c6eb-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="1c6eb-111">Serwer jest zintegrowany z [ASP.NET Core routingiem punktów końcowych](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="1c6eb-112">Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za pomocą <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> programu w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1c6eb-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="1c6eb-113">Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do Razor strony, które pełnią rolę hosta dla części aplikacji serwerowej po stronie serwera Blazor .</span><span class="sxs-lookup"><span data-stu-id="1c6eb-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="1c6eb-114">Zgodnie z Konwencją strona *hosta* ma zwykle nazwę *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="1c6eb-115">Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="1c6eb-116">Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="1c6eb-117">Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania działania Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="1c6eb-118">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="1c6eb-118">Route templates</span></span>

<span data-ttu-id="1c6eb-119"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia kierowanie do każdego składnika z określoną trasą.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="1c6eb-120"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik pojawi się w pliku *App. Razor* :</span><span class="sxs-lookup"><span data-stu-id="1c6eb-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="1c6eb-121">Gdy plik *Razor* z `@page` dyrektywą jest kompilowany, wygenerowana Klasa jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="1c6eb-122">W środowisku uruchomieniowym <xref:Microsoft.AspNetCore.Components.RouteView> składnik:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="1c6eb-123">Odbiera <xref:Microsoft.AspNetCore.Components.RouteData> od siebie <xref:Microsoft.AspNetCore.Components.Routing.Router> wraz z dowolnymi żądanymi parametrami.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="1c6eb-124">Renderuje określony składnik za pomocą układu (lub opcjonalnego układu domyślnego) przy użyciu określonych parametrów.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="1c6eb-125">Opcjonalnie można określić <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr z klasą układu, która ma być używana dla składników, które nie określają układu.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="1c6eb-126">Domyślne Blazor Szablony określają `MainLayout` składnik.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="1c6eb-127">*MainLayout. Razor* znajduje się w folderze *udostępnionym* projektu szablonu.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="1c6eb-128">Aby uzyskać więcej informacji na temat układów, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="1c6eb-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="1c6eb-129">Do składnika można zastosować wiele szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="1c6eb-130">Poniższy składnik odpowiada na żądania dla `/BlazorRoute` i `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="1c6eb-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="1c6eb-131">Aby można było poprawnie rozwiązać adresy URL, aplikacja musi zawierać `<base>` tag w pliku *wwwroot/index.html* ( Blazor Webassembly) lub *Pages/_Host. cshtml* ( Blazor serwer) z ścieżką bazową aplikacji określoną w `href` atrybucie ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="1c6eb-132">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="1c6eb-133">Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości</span><span class="sxs-lookup"><span data-stu-id="1c6eb-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="1c6eb-134"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="1c6eb-135">W pliku *App. Razor* Ustaw zawartość niestandardową w <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametrze szablonu <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="1c6eb-136">Zawartość `<NotFound>` tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="1c6eb-137">Aby zastosować domyślny układ do <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> zawartości, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="1c6eb-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="1c6eb-138">Kierowanie do składników z wielu zestawów</span><span class="sxs-lookup"><span data-stu-id="1c6eb-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="1c6eb-139">Użyj <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametru, aby określić dodatkowe zestawy dla <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika do uwzględnienia podczas wyszukiwania składników routingu.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="1c6eb-140">Określone zestawy są traktowane jako uzupełnienie `AppAssembly` określonego zestawu.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="1c6eb-141">W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w bibliotece klas, do której się odwołuje.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="1c6eb-142">W poniższym <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> przykładzie przedstawiono obsługę routingu dla `Component1` :</span><span class="sxs-lookup"><span data-stu-id="1c6eb-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="1c6eb-143">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="1c6eb-143">Route parameters</span></span>

<span data-ttu-id="1c6eb-144">Router używa parametrów trasy do wypełniania odpowiednich parametrów składnika o tej samej nazwie (bez uwzględniania wielkości liter):</span><span class="sxs-lookup"><span data-stu-id="1c6eb-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="1c6eb-145">Parametry opcjonalne nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="1c6eb-146">`@page`W poprzednim przykładzie są stosowane dwie dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="1c6eb-147">Pierwszy zezwala na nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="1c6eb-148">Druga `@page` dyrektywa przyjmuje `{text}` parametr Route i przypisuje wartość do `Text` właściwości.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="1c6eb-149">Ograniczenia trasy</span><span class="sxs-lookup"><span data-stu-id="1c6eb-149">Route constraints</span></span>

<span data-ttu-id="1c6eb-150">Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="1c6eb-151">W poniższym przykładzie trasa do `Users` składnika dopasowuje się tylko wtedy, gdy:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="1c6eb-152">`Id`Segment trasy jest obecny w adresie URL żądania.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="1c6eb-153">`Id`Segment jest liczbą całkowitą ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="1c6eb-154">Dostępne są ograniczenia trasy podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="1c6eb-155">W przypadku ograniczeń trasy, które pasują do niezmiennej kultury, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="1c6eb-156">Typu</span><span class="sxs-lookup"><span data-stu-id="1c6eb-156">Constraint</span></span> | <span data-ttu-id="1c6eb-157">Przykład</span><span class="sxs-lookup"><span data-stu-id="1c6eb-157">Example</span></span>           | <span data-ttu-id="1c6eb-158">Przykładowe dopasowania</span><span class="sxs-lookup"><span data-stu-id="1c6eb-158">Example Matches</span></span>                                                                  | <span data-ttu-id="1c6eb-159">Niezmiennej</span><span class="sxs-lookup"><span data-stu-id="1c6eb-159">Invariant</span></span><br><span data-ttu-id="1c6eb-160">kultura</span><span class="sxs-lookup"><span data-stu-id="1c6eb-160">culture</span></span><br><span data-ttu-id="1c6eb-161">parowanie</span><span class="sxs-lookup"><span data-stu-id="1c6eb-161">matching</span></span> |
| ---
<span data-ttu-id="1c6eb-162">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-163">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-164">'Identity'</span></span>
- <span data-ttu-id="1c6eb-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-166">'Razor'</span></span>
- <span data-ttu-id="1c6eb-167">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-168">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-169">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-170">'Identity'</span></span>
- <span data-ttu-id="1c6eb-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-172">'Razor'</span></span>
- <span data-ttu-id="1c6eb-173">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-174">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-175">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-176">'Identity'</span></span>
- <span data-ttu-id="1c6eb-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-178">'Razor'</span></span>
- <span data-ttu-id="1c6eb-179">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-179">'SignalR' uid:</span></span> 

<span data-ttu-id="1c6eb-180">----- | ---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-181">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-182">'Identity'</span></span>
- <span data-ttu-id="1c6eb-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-184">'Razor'</span></span>
- <span data-ttu-id="1c6eb-185">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-186">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-187">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-188">'Identity'</span></span>
- <span data-ttu-id="1c6eb-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-190">'Razor'</span></span>
- <span data-ttu-id="1c6eb-191">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-192">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-193">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-194">'Identity'</span></span>
- <span data-ttu-id="1c6eb-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-196">'Razor'</span></span>
- <span data-ttu-id="1c6eb-197">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-198">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-199">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-200">'Identity'</span></span>
- <span data-ttu-id="1c6eb-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-202">'Razor'</span></span>
- <span data-ttu-id="1c6eb-203">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-204">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-205">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-206">'Identity'</span></span>
- <span data-ttu-id="1c6eb-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-208">'Razor'</span></span>
- <span data-ttu-id="1c6eb-209">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-210">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-211">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-212">'Identity'</span></span>
- <span data-ttu-id="1c6eb-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-214">'Razor'</span></span>
- <span data-ttu-id="1c6eb-215">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-215">'SignalR' uid:</span></span> 

<span data-ttu-id="1c6eb-216">--------- | ---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-217">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-218">'Identity'</span></span>
- <span data-ttu-id="1c6eb-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-220">'Razor'</span></span>
- <span data-ttu-id="1c6eb-221">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-222">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-223">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-224">'Identity'</span></span>
- <span data-ttu-id="1c6eb-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-226">'Razor'</span></span>
- <span data-ttu-id="1c6eb-227">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-228">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-229">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-230">'Identity'</span></span>
- <span data-ttu-id="1c6eb-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-232">'Razor'</span></span>
- <span data-ttu-id="1c6eb-233">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-234">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-235">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-236">'Identity'</span></span>
- <span data-ttu-id="1c6eb-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-238">'Razor'</span></span>
- <span data-ttu-id="1c6eb-239">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-240">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-241">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-242">'Identity'</span></span>
- <span data-ttu-id="1c6eb-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-244">'Razor'</span></span>
- <span data-ttu-id="1c6eb-245">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-246">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-247">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-248">'Identity'</span></span>
- <span data-ttu-id="1c6eb-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-250">'Razor'</span></span>
- <span data-ttu-id="1c6eb-251">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-252">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-253">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-254">'Identity'</span></span>
- <span data-ttu-id="1c6eb-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-256">'Razor'</span></span>
- <span data-ttu-id="1c6eb-257">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-258">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-259">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-260">'Identity'</span></span>
- <span data-ttu-id="1c6eb-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-262">'Razor'</span></span>
- <span data-ttu-id="1c6eb-263">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-264">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-265">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-266">'Identity'</span></span>
- <span data-ttu-id="1c6eb-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-268">'Razor'</span></span>
- <span data-ttu-id="1c6eb-269">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-270">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-271">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-272">'Identity'</span></span>
- <span data-ttu-id="1c6eb-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-274">'Razor'</span></span>
- <span data-ttu-id="1c6eb-275">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-276">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-277">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-278">'Identity'</span></span>
- <span data-ttu-id="1c6eb-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-280">'Razor'</span></span>
- <span data-ttu-id="1c6eb-281">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-282">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-283">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-284">'Identity'</span></span>
- <span data-ttu-id="1c6eb-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-286">'Razor'</span></span>
- <span data-ttu-id="1c6eb-287">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-288">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-289">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-290">'Identity'</span></span>
- <span data-ttu-id="1c6eb-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-292">'Razor'</span></span>
- <span data-ttu-id="1c6eb-293">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-294">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-295">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-296">'Identity'</span></span>
- <span data-ttu-id="1c6eb-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-298">'Razor'</span></span>
- <span data-ttu-id="1c6eb-299">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-300">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-301">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-302">'Identity'</span></span>
- <span data-ttu-id="1c6eb-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-304">'Razor'</span></span>
- <span data-ttu-id="1c6eb-305">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-306">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-307">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-308">'Identity'</span></span>
- <span data-ttu-id="1c6eb-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-310">'Razor'</span></span>
- <span data-ttu-id="1c6eb-311">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-312">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-313">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-314">'Identity'</span></span>
- <span data-ttu-id="1c6eb-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-316">'Razor'</span></span>
- <span data-ttu-id="1c6eb-317">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-318">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-319">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-320">'Identity'</span></span>
- <span data-ttu-id="1c6eb-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-322">'Razor'</span></span>
- <span data-ttu-id="1c6eb-323">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-324">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-325">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-326">'Identity'</span></span>
- <span data-ttu-id="1c6eb-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-328">'Razor'</span></span>
- <span data-ttu-id="1c6eb-329">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-330">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-331">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-332">'Identity'</span></span>
- <span data-ttu-id="1c6eb-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-334">'Razor'</span></span>
- <span data-ttu-id="1c6eb-335">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-336">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-337">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-338">'Identity'</span></span>
- <span data-ttu-id="1c6eb-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-340">'Razor'</span></span>
- <span data-ttu-id="1c6eb-341">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-342">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-343">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-344">'Identity'</span></span>
- <span data-ttu-id="1c6eb-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-346">'Razor'</span></span>
- <span data-ttu-id="1c6eb-347">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-348">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-349">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-350">'Identity'</span></span>
- <span data-ttu-id="1c6eb-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-352">'Razor'</span></span>
- <span data-ttu-id="1c6eb-353">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-354">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-355">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-356">'Identity'</span></span>
- <span data-ttu-id="1c6eb-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-358">'Razor'</span></span>
- <span data-ttu-id="1c6eb-359">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-360">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-361">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-362">'Identity'</span></span>
- <span data-ttu-id="1c6eb-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-364">'Razor'</span></span>
- <span data-ttu-id="1c6eb-365">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-366">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-367">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-368">'Identity'</span></span>
- <span data-ttu-id="1c6eb-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-370">'Razor'</span></span>
- <span data-ttu-id="1c6eb-371">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-372">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-373">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-374">'Identity'</span></span>
- <span data-ttu-id="1c6eb-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-376">'Razor'</span></span>
- <span data-ttu-id="1c6eb-377">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-378">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-379">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-380">'Identity'</span></span>
- <span data-ttu-id="1c6eb-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-382">'Razor'</span></span>
- <span data-ttu-id="1c6eb-383">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-384">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-385">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-386">'Identity'</span></span>
- <span data-ttu-id="1c6eb-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-388">'Razor'</span></span>
- <span data-ttu-id="1c6eb-389">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-390">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-391">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-392">'Identity'</span></span>
- <span data-ttu-id="1c6eb-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-394">'Razor'</span></span>
- <span data-ttu-id="1c6eb-395">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-396">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-397">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-398">'Identity'</span></span>
- <span data-ttu-id="1c6eb-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-400">'Razor'</span></span>
- <span data-ttu-id="1c6eb-401">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-402">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-403">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-404">'Identity'</span></span>
- <span data-ttu-id="1c6eb-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-406">'Razor'</span></span>
- <span data-ttu-id="1c6eb-407">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-408">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-409">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-410">'Identity'</span></span>
- <span data-ttu-id="1c6eb-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-412">'Razor'</span></span>
- <span data-ttu-id="1c6eb-413">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-414">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-415">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-416">'Identity'</span></span>
- <span data-ttu-id="1c6eb-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-418">'Razor'</span></span>
- <span data-ttu-id="1c6eb-419">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-420">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-421">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-422">'Identity'</span></span>
- <span data-ttu-id="1c6eb-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-424">'Razor'</span></span>
- <span data-ttu-id="1c6eb-425">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-426">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-427">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-428">'Identity'</span></span>
- <span data-ttu-id="1c6eb-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-430">'Razor'</span></span>
- <span data-ttu-id="1c6eb-431">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-432">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-433">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-434">'Identity'</span></span>
- <span data-ttu-id="1c6eb-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-436">'Razor'</span></span>
- <span data-ttu-id="1c6eb-437">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-438">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-439">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-440">'Identity'</span></span>
- <span data-ttu-id="1c6eb-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-442">'Razor'</span></span>
- <span data-ttu-id="1c6eb-443">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-443">'SignalR' uid:</span></span> 

<span data-ttu-id="1c6eb-444">---------------------------------------- | :---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-445">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-446">'Identity'</span></span>
- <span data-ttu-id="1c6eb-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-448">'Razor'</span></span>
- <span data-ttu-id="1c6eb-449">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-450">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-451">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-452">'Identity'</span></span>
- <span data-ttu-id="1c6eb-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-454">'Razor'</span></span>
- <span data-ttu-id="1c6eb-455">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-456">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-457">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-458">'Identity'</span></span>
- <span data-ttu-id="1c6eb-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-460">'Razor'</span></span>
- <span data-ttu-id="1c6eb-461">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-462">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-463">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-464">'Identity'</span></span>
- <span data-ttu-id="1c6eb-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-466">'Razor'</span></span>
- <span data-ttu-id="1c6eb-467">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-468">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-469">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-470">'Identity'</span></span>
- <span data-ttu-id="1c6eb-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-472">'Razor'</span></span>
- <span data-ttu-id="1c6eb-473">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-474">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-475">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-476">'Identity'</span></span>
- <span data-ttu-id="1c6eb-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-478">'Razor'</span></span>
- <span data-ttu-id="1c6eb-479">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-480">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-481">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-482">'Identity'</span></span>
- <span data-ttu-id="1c6eb-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-484">'Razor'</span></span>
- <span data-ttu-id="1c6eb-485">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-486">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-487">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-488">'Identity'</span></span>
- <span data-ttu-id="1c6eb-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-490">'Razor'</span></span>
- <span data-ttu-id="1c6eb-491">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-492">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-493">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-494">'Identity'</span></span>
- <span data-ttu-id="1c6eb-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-496">'Razor'</span></span>
- <span data-ttu-id="1c6eb-497">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-498">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-499">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-500">'Identity'</span></span>
- <span data-ttu-id="1c6eb-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-502">'Razor'</span></span>
- <span data-ttu-id="1c6eb-503">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-504">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-505">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-506">'Identity'</span></span>
- <span data-ttu-id="1c6eb-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-508">'Razor'</span></span>
- <span data-ttu-id="1c6eb-509">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-510">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-511">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-512">'Identity'</span></span>
- <span data-ttu-id="1c6eb-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-514">'Razor'</span></span>
- <span data-ttu-id="1c6eb-515">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-516">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-517">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-518">'Identity'</span></span>
- <span data-ttu-id="1c6eb-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-520">'Razor'</span></span>
- <span data-ttu-id="1c6eb-521">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-521">'SignalR' uid:</span></span> 

<span data-ttu-id="1c6eb-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Nie | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Tak | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Tak | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Tak | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Tak | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Nie | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Tak | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Tak |</span><span class="sxs-lookup"><span data-stu-id="1c6eb-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="1c6eb-523">Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub <xref:System.DateTime> ), zawsze używają niezmiennej kultury.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="1c6eb-524">W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="1c6eb-525">Routing z adresami URL zawierającymi kropki</span><span class="sxs-lookup"><span data-stu-id="1c6eb-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="1c6eb-526">W obszarze Blazor aplikacje serwera domyślna trasa w *_Host. cshtml* to `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="1c6eb-527">Adres URL żądania, który zawiera kropkę ( `.` ) nie pasuje do trasy domyślnej, ponieważ adres URL wygląda na żądanie pliku.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="1c6eb-528">BlazorAplikacja zwraca *404 — nie odnaleziono* odpowiedzi dla pliku statycznego, który nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="1c6eb-529">Aby użyć tras zawierających kropkę, skonfiguruj *_Host. cshtml* przy użyciu następującego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="1c6eb-530">`"/{**path}"`Szablon zawiera:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="1c6eb-531">Podwójna gwiazdka *catch-all* ( `**` ) do przechwytywania ścieżki między wieloma granicami folderów bez kodowania ukośników ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="1c6eb-532">`path`Nazwa parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="1c6eb-533">Składnia *catch-all* parametru ( `*` / `**` ) **nie** jest obsługiwana w Razor składnikach (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="1c6eb-534">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="1c6eb-535">Składnik NavLink</span><span class="sxs-lookup"><span data-stu-id="1c6eb-535">NavLink component</span></span>

<span data-ttu-id="1c6eb-536"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Podczas tworzenia linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML ( `<a>` ).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="1c6eb-537"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia `active` klasy CSS w zależności od tego, czy jest on `href` zgodny z bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="1c6eb-538">`active`Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="1c6eb-539">Poniższy `NavMenu` składnik tworzy pasek nawigacyjny [Bootstrap](https://getbootstrap.com/docs/) , który pokazuje, jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składników:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="1c6eb-540">Dostępne są dwie <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="1c6eb-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Jest aktywny, gdy jest zgodny z całym bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="1c6eb-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*ustawienie domyślne*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> jest aktywny, gdy pasuje do dowolnego PREFIKSU bieżącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="1c6eb-543">W poprzednim przykładzie Strona główna <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` odpowiada GŁÓWNEmu adresowi URL i odbiera tylko `active` klasy CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="1c6eb-544">Druga <xref:Microsoft.AspNetCore.Components.Routing.NavLink> otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `MyComponent` prefiksem (na przykład `https://localhost:5001/MyComponent` i `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="1c6eb-545">Dodatkowe <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="1c6eb-546">W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnik zawiera `target` atrybut:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="1c6eb-547">Renderuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="1c6eb-548">Pomoc dotycząca stanu identyfikatora URI i nawigacji</span><span class="sxs-lookup"><span data-stu-id="1c6eb-548">URI and navigation state helpers</span></span>

<span data-ttu-id="1c6eb-549">Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacją w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="1c6eb-550"><xref:Microsoft.AspNetCore.Components.NavigationManager>zawiera zdarzenie i metody przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="1c6eb-551">Członek</span><span class="sxs-lookup"><span data-stu-id="1c6eb-551">Member</span></span> | <span data-ttu-id="1c6eb-552">Opis</span><span class="sxs-lookup"><span data-stu-id="1c6eb-552">Description</span></span> |
| ---
<span data-ttu-id="1c6eb-553">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-554">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-555">'Identity'</span></span>
- <span data-ttu-id="1c6eb-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-557">'Razor'</span></span>
- <span data-ttu-id="1c6eb-558">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-558">'SignalR' uid:</span></span> 

<span data-ttu-id="1c6eb-559">--- | ---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-560">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-561">'Identity'</span></span>
- <span data-ttu-id="1c6eb-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-563">'Razor'</span></span>
- <span data-ttu-id="1c6eb-564">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-565">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-566">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-567">'Identity'</span></span>
- <span data-ttu-id="1c6eb-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-569">'Razor'</span></span>
- <span data-ttu-id="1c6eb-570">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c6eb-571">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c6eb-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-572">'Blazor'</span></span>
- <span data-ttu-id="1c6eb-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-573">'Identity'</span></span>
- <span data-ttu-id="1c6eb-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c6eb-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c6eb-575">'Razor'</span></span>
- <span data-ttu-id="1c6eb-576">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-576">'SignalR' uid:</span></span> 

<span data-ttu-id="1c6eb-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Pobiera bieżący bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="1c6eb-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="1c6eb-579">Zazwyczaj <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpowiada `href` atrybutowi `<base>` elementu dokumentu w *wwwroot/index.html* ( Blazor webassembly) lub *Pages/_Host. cshtml* ( Blazor serwer).</span><span class="sxs-lookup"><span data-stu-id="1c6eb-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="1c6eb-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Przechodzi do określonego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="1c6eb-581">Jeśli `forceLoad` jest `true` :</span><span class="sxs-lookup"><span data-stu-id="1c6eb-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="1c6eb-582">Routing po stronie klienta jest pomijany.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="1c6eb-583">W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="1c6eb-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="1c6eb-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Konwertuje względny identyfikator URI na bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="1c6eb-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej przez <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="1c6eb-587">Poniższy składnik przechodzi do `Counter` składnika aplikacji po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="1c6eb-588">Poniższy składnik obsługuje zdarzenie zmiany lokalizacji przez ustawienie <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1c6eb-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1c6eb-589">Metoda jest odłączana, `HandleLocationChanged` gdy `Dispose` jest wywoływana przez platformę.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="1c6eb-590">Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-590">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="1c6eb-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>zawiera następujące informacje o zdarzeniu:</span><span class="sxs-lookup"><span data-stu-id="1c6eb-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="1c6eb-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adres URL nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="1c6eb-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Jeśli `true` Blazor przechwytuje nawigację z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="1c6eb-594">Jeśli `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wystąpiła nawigacja.</span><span class="sxs-lookup"><span data-stu-id="1c6eb-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="1c6eb-595">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="1c6eb-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
