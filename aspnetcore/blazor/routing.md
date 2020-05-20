---
<span data-ttu-id="5061a-101">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-102">'Blazor'</span></span>
- <span data-ttu-id="5061a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-103">'Identity'</span></span>
- <span data-ttu-id="5061a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-105">'Razor'</span></span>
- <span data-ttu-id="5061a-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="5061a-107">BlazorRouting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5061a-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="5061a-108">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5061a-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5061a-109">Dowiedz się, jak kierować żądania i jak używać `NavLink` składnika do tworzenia linków nawigacji w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="5061a-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="5061a-110">ASP.NET Core integracja z routingiem punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="5061a-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="5061a-111">Serwer jest zintegrowany z [ASP.NET Core routingiem punktów końcowych](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="5061a-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="5061a-112">Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za pomocą `MapBlazorHub` programu w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="5061a-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="5061a-113">Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do Razor strony, które pełnią rolę hosta dla części aplikacji serwerowej po stronie serwera Blazor .</span><span class="sxs-lookup"><span data-stu-id="5061a-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="5061a-114">Zgodnie z Konwencją strona *hosta* ma zwykle nazwę *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5061a-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="5061a-115">Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras.</span><span class="sxs-lookup"><span data-stu-id="5061a-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="5061a-116">Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="5061a-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="5061a-117">Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania działania Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="5061a-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="5061a-118">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="5061a-118">Route templates</span></span>

<span data-ttu-id="5061a-119">`Router`Składnik umożliwia kierowanie do każdego składnika z określoną trasą.</span><span class="sxs-lookup"><span data-stu-id="5061a-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="5061a-120">`Router`Składnik pojawi się w pliku *App. Razor* :</span><span class="sxs-lookup"><span data-stu-id="5061a-120">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="5061a-121">Gdy plik *Razor* z `@page` dyrektywą jest kompilowany, wygenerowana Klasa jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="5061a-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="5061a-122">W środowisku uruchomieniowym `RouteView` składnik:</span><span class="sxs-lookup"><span data-stu-id="5061a-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="5061a-123">Odbiera `RouteData` od siebie `Router` wraz z dowolnymi żądanymi parametrami.</span><span class="sxs-lookup"><span data-stu-id="5061a-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="5061a-124">Renderuje określony składnik za pomocą układu (lub opcjonalnego układu domyślnego) przy użyciu określonych parametrów.</span><span class="sxs-lookup"><span data-stu-id="5061a-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="5061a-125">Opcjonalnie można określić `DefaultLayout` parametr z klasą układu, która ma być używana dla składników, które nie określają układu.</span><span class="sxs-lookup"><span data-stu-id="5061a-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="5061a-126">Domyślne Blazor Szablony określają `MainLayout` składnik.</span><span class="sxs-lookup"><span data-stu-id="5061a-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="5061a-127">*MainLayout. Razor* znajduje się w folderze *udostępnionym* projektu szablonu.</span><span class="sxs-lookup"><span data-stu-id="5061a-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="5061a-128">Aby uzyskać więcej informacji na temat układów, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="5061a-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="5061a-129">Do składnika można zastosować wiele szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="5061a-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="5061a-130">Poniższy składnik odpowiada na żądania dla `/BlazorRoute` i `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="5061a-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="5061a-131">Aby można było poprawnie rozwiązać adresy URL, aplikacja musi zawierać `<base>` tag w pliku *wwwroot/index.html* ( Blazor Webassembly) lub *Pages/_Host. cshtml* ( Blazor serwer) z ścieżką bazową aplikacji określoną w `href` atrybucie ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="5061a-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="5061a-132">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="5061a-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="5061a-133">Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości</span><span class="sxs-lookup"><span data-stu-id="5061a-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="5061a-134">`Router`Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.</span><span class="sxs-lookup"><span data-stu-id="5061a-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="5061a-135">W pliku *App. Razor* Ustaw zawartość niestandardową w `NotFound` parametrze szablonu `Router` składnika:</span><span class="sxs-lookup"><span data-stu-id="5061a-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="5061a-136">Zawartość `<NotFound>` tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="5061a-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="5061a-137">Aby zastosować domyślny układ do `NotFound` zawartości, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="5061a-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="5061a-138">Kierowanie do składników z wielu zestawów</span><span class="sxs-lookup"><span data-stu-id="5061a-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="5061a-139">Użyj `AdditionalAssemblies` parametru, aby określić dodatkowe zestawy dla `Router` składnika do uwzględnienia podczas wyszukiwania składników routingu.</span><span class="sxs-lookup"><span data-stu-id="5061a-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="5061a-140">Określone zestawy są traktowane jako uzupełnienie `AppAssembly` określonego zestawu.</span><span class="sxs-lookup"><span data-stu-id="5061a-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="5061a-141">W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w bibliotece klas, do której się odwołuje.</span><span class="sxs-lookup"><span data-stu-id="5061a-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="5061a-142">W poniższym `AdditionalAssemblies` przykładzie przedstawiono obsługę routingu dla `Component1` :</span><span class="sxs-lookup"><span data-stu-id="5061a-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="5061a-143">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="5061a-143">Route parameters</span></span>

<span data-ttu-id="5061a-144">Router używa parametrów trasy do wypełniania odpowiednich parametrów składnika o tej samej nazwie (bez uwzględniania wielkości liter):</span><span class="sxs-lookup"><span data-stu-id="5061a-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="5061a-145">Parametry opcjonalne nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="5061a-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="5061a-146">`@page`W poprzednim przykładzie są stosowane dwie dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="5061a-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="5061a-147">Pierwszy zezwala na nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="5061a-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="5061a-148">Druga `@page` dyrektywa przyjmuje `{text}` parametr Route i przypisuje wartość do `Text` właściwości.</span><span class="sxs-lookup"><span data-stu-id="5061a-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="5061a-149">Ograniczenia trasy</span><span class="sxs-lookup"><span data-stu-id="5061a-149">Route constraints</span></span>

<span data-ttu-id="5061a-150">Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.</span><span class="sxs-lookup"><span data-stu-id="5061a-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="5061a-151">W poniższym przykładzie trasa do `Users` składnika dopasowuje się tylko wtedy, gdy:</span><span class="sxs-lookup"><span data-stu-id="5061a-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="5061a-152">`Id`Segment trasy jest obecny w adresie URL żądania.</span><span class="sxs-lookup"><span data-stu-id="5061a-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="5061a-153">`Id`Segment jest liczbą całkowitą ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="5061a-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="5061a-154">Dostępne są ograniczenia trasy podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="5061a-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="5061a-155">W przypadku ograniczeń trasy, które pasują do niezmiennej kultury, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="5061a-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="5061a-156">Typu</span><span class="sxs-lookup"><span data-stu-id="5061a-156">Constraint</span></span> | <span data-ttu-id="5061a-157">Przykład</span><span class="sxs-lookup"><span data-stu-id="5061a-157">Example</span></span>           | <span data-ttu-id="5061a-158">Przykładowe dopasowania</span><span class="sxs-lookup"><span data-stu-id="5061a-158">Example Matches</span></span>                                                                  | <span data-ttu-id="5061a-159">Niezmiennej</span><span class="sxs-lookup"><span data-stu-id="5061a-159">Invariant</span></span><br><span data-ttu-id="5061a-160">kultura</span><span class="sxs-lookup"><span data-stu-id="5061a-160">culture</span></span><br><span data-ttu-id="5061a-161">parowanie</span><span class="sxs-lookup"><span data-stu-id="5061a-161">matching</span></span> |
| ---
<span data-ttu-id="5061a-162">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-163">'Blazor'</span></span>
- <span data-ttu-id="5061a-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-164">'Identity'</span></span>
- <span data-ttu-id="5061a-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-166">'Razor'</span></span>
- <span data-ttu-id="5061a-167">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-168">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-169">'Blazor'</span></span>
- <span data-ttu-id="5061a-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-170">'Identity'</span></span>
- <span data-ttu-id="5061a-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-172">'Razor'</span></span>
- <span data-ttu-id="5061a-173">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-174">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-175">'Blazor'</span></span>
- <span data-ttu-id="5061a-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-176">'Identity'</span></span>
- <span data-ttu-id="5061a-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-178">'Razor'</span></span>
- <span data-ttu-id="5061a-179">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-179">'SignalR' uid:</span></span> 

<span data-ttu-id="5061a-180">----- | ---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-181">'Blazor'</span></span>
- <span data-ttu-id="5061a-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-182">'Identity'</span></span>
- <span data-ttu-id="5061a-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-184">'Razor'</span></span>
- <span data-ttu-id="5061a-185">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-186">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-187">'Blazor'</span></span>
- <span data-ttu-id="5061a-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-188">'Identity'</span></span>
- <span data-ttu-id="5061a-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-190">'Razor'</span></span>
- <span data-ttu-id="5061a-191">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-192">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-193">'Blazor'</span></span>
- <span data-ttu-id="5061a-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-194">'Identity'</span></span>
- <span data-ttu-id="5061a-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-196">'Razor'</span></span>
- <span data-ttu-id="5061a-197">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-198">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-199">'Blazor'</span></span>
- <span data-ttu-id="5061a-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-200">'Identity'</span></span>
- <span data-ttu-id="5061a-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-202">'Razor'</span></span>
- <span data-ttu-id="5061a-203">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-204">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-205">'Blazor'</span></span>
- <span data-ttu-id="5061a-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-206">'Identity'</span></span>
- <span data-ttu-id="5061a-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-208">'Razor'</span></span>
- <span data-ttu-id="5061a-209">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-210">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-211">'Blazor'</span></span>
- <span data-ttu-id="5061a-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-212">'Identity'</span></span>
- <span data-ttu-id="5061a-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-214">'Razor'</span></span>
- <span data-ttu-id="5061a-215">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-215">'SignalR' uid:</span></span> 

<span data-ttu-id="5061a-216">--------- | ---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-217">'Blazor'</span></span>
- <span data-ttu-id="5061a-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-218">'Identity'</span></span>
- <span data-ttu-id="5061a-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-220">'Razor'</span></span>
- <span data-ttu-id="5061a-221">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-222">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-223">'Blazor'</span></span>
- <span data-ttu-id="5061a-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-224">'Identity'</span></span>
- <span data-ttu-id="5061a-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-226">'Razor'</span></span>
- <span data-ttu-id="5061a-227">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-228">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-229">'Blazor'</span></span>
- <span data-ttu-id="5061a-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-230">'Identity'</span></span>
- <span data-ttu-id="5061a-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-232">'Razor'</span></span>
- <span data-ttu-id="5061a-233">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-234">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-235">'Blazor'</span></span>
- <span data-ttu-id="5061a-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-236">'Identity'</span></span>
- <span data-ttu-id="5061a-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-238">'Razor'</span></span>
- <span data-ttu-id="5061a-239">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-240">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-241">'Blazor'</span></span>
- <span data-ttu-id="5061a-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-242">'Identity'</span></span>
- <span data-ttu-id="5061a-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-244">'Razor'</span></span>
- <span data-ttu-id="5061a-245">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-246">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-247">'Blazor'</span></span>
- <span data-ttu-id="5061a-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-248">'Identity'</span></span>
- <span data-ttu-id="5061a-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-250">'Razor'</span></span>
- <span data-ttu-id="5061a-251">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-252">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-253">'Blazor'</span></span>
- <span data-ttu-id="5061a-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-254">'Identity'</span></span>
- <span data-ttu-id="5061a-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-256">'Razor'</span></span>
- <span data-ttu-id="5061a-257">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-258">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-259">'Blazor'</span></span>
- <span data-ttu-id="5061a-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-260">'Identity'</span></span>
- <span data-ttu-id="5061a-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-262">'Razor'</span></span>
- <span data-ttu-id="5061a-263">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-264">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-265">'Blazor'</span></span>
- <span data-ttu-id="5061a-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-266">'Identity'</span></span>
- <span data-ttu-id="5061a-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-268">'Razor'</span></span>
- <span data-ttu-id="5061a-269">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-270">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-271">'Blazor'</span></span>
- <span data-ttu-id="5061a-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-272">'Identity'</span></span>
- <span data-ttu-id="5061a-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-274">'Razor'</span></span>
- <span data-ttu-id="5061a-275">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-276">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-277">'Blazor'</span></span>
- <span data-ttu-id="5061a-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-278">'Identity'</span></span>
- <span data-ttu-id="5061a-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-280">'Razor'</span></span>
- <span data-ttu-id="5061a-281">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-282">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-283">'Blazor'</span></span>
- <span data-ttu-id="5061a-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-284">'Identity'</span></span>
- <span data-ttu-id="5061a-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-286">'Razor'</span></span>
- <span data-ttu-id="5061a-287">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-288">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-289">'Blazor'</span></span>
- <span data-ttu-id="5061a-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-290">'Identity'</span></span>
- <span data-ttu-id="5061a-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-292">'Razor'</span></span>
- <span data-ttu-id="5061a-293">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-294">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-295">'Blazor'</span></span>
- <span data-ttu-id="5061a-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-296">'Identity'</span></span>
- <span data-ttu-id="5061a-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-298">'Razor'</span></span>
- <span data-ttu-id="5061a-299">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-300">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-301">'Blazor'</span></span>
- <span data-ttu-id="5061a-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-302">'Identity'</span></span>
- <span data-ttu-id="5061a-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-304">'Razor'</span></span>
- <span data-ttu-id="5061a-305">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-306">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-307">'Blazor'</span></span>
- <span data-ttu-id="5061a-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-308">'Identity'</span></span>
- <span data-ttu-id="5061a-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-310">'Razor'</span></span>
- <span data-ttu-id="5061a-311">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-312">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-313">'Blazor'</span></span>
- <span data-ttu-id="5061a-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-314">'Identity'</span></span>
- <span data-ttu-id="5061a-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-316">'Razor'</span></span>
- <span data-ttu-id="5061a-317">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-318">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-319">'Blazor'</span></span>
- <span data-ttu-id="5061a-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-320">'Identity'</span></span>
- <span data-ttu-id="5061a-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-322">'Razor'</span></span>
- <span data-ttu-id="5061a-323">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-324">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-325">'Blazor'</span></span>
- <span data-ttu-id="5061a-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-326">'Identity'</span></span>
- <span data-ttu-id="5061a-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-328">'Razor'</span></span>
- <span data-ttu-id="5061a-329">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-330">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-331">'Blazor'</span></span>
- <span data-ttu-id="5061a-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-332">'Identity'</span></span>
- <span data-ttu-id="5061a-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-334">'Razor'</span></span>
- <span data-ttu-id="5061a-335">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-336">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-337">'Blazor'</span></span>
- <span data-ttu-id="5061a-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-338">'Identity'</span></span>
- <span data-ttu-id="5061a-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-340">'Razor'</span></span>
- <span data-ttu-id="5061a-341">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-342">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-343">'Blazor'</span></span>
- <span data-ttu-id="5061a-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-344">'Identity'</span></span>
- <span data-ttu-id="5061a-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-346">'Razor'</span></span>
- <span data-ttu-id="5061a-347">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-348">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-349">'Blazor'</span></span>
- <span data-ttu-id="5061a-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-350">'Identity'</span></span>
- <span data-ttu-id="5061a-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-352">'Razor'</span></span>
- <span data-ttu-id="5061a-353">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-354">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-355">'Blazor'</span></span>
- <span data-ttu-id="5061a-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-356">'Identity'</span></span>
- <span data-ttu-id="5061a-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-358">'Razor'</span></span>
- <span data-ttu-id="5061a-359">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-360">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-361">'Blazor'</span></span>
- <span data-ttu-id="5061a-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-362">'Identity'</span></span>
- <span data-ttu-id="5061a-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-364">'Razor'</span></span>
- <span data-ttu-id="5061a-365">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-366">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-367">'Blazor'</span></span>
- <span data-ttu-id="5061a-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-368">'Identity'</span></span>
- <span data-ttu-id="5061a-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-370">'Razor'</span></span>
- <span data-ttu-id="5061a-371">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-372">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-373">'Blazor'</span></span>
- <span data-ttu-id="5061a-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-374">'Identity'</span></span>
- <span data-ttu-id="5061a-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-376">'Razor'</span></span>
- <span data-ttu-id="5061a-377">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-378">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-379">'Blazor'</span></span>
- <span data-ttu-id="5061a-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-380">'Identity'</span></span>
- <span data-ttu-id="5061a-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-382">'Razor'</span></span>
- <span data-ttu-id="5061a-383">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-384">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-385">'Blazor'</span></span>
- <span data-ttu-id="5061a-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-386">'Identity'</span></span>
- <span data-ttu-id="5061a-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-388">'Razor'</span></span>
- <span data-ttu-id="5061a-389">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-390">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-391">'Blazor'</span></span>
- <span data-ttu-id="5061a-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-392">'Identity'</span></span>
- <span data-ttu-id="5061a-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-394">'Razor'</span></span>
- <span data-ttu-id="5061a-395">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-396">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-397">'Blazor'</span></span>
- <span data-ttu-id="5061a-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-398">'Identity'</span></span>
- <span data-ttu-id="5061a-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-400">'Razor'</span></span>
- <span data-ttu-id="5061a-401">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-402">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-403">'Blazor'</span></span>
- <span data-ttu-id="5061a-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-404">'Identity'</span></span>
- <span data-ttu-id="5061a-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-406">'Razor'</span></span>
- <span data-ttu-id="5061a-407">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-408">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-409">'Blazor'</span></span>
- <span data-ttu-id="5061a-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-410">'Identity'</span></span>
- <span data-ttu-id="5061a-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-412">'Razor'</span></span>
- <span data-ttu-id="5061a-413">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-414">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-415">'Blazor'</span></span>
- <span data-ttu-id="5061a-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-416">'Identity'</span></span>
- <span data-ttu-id="5061a-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-418">'Razor'</span></span>
- <span data-ttu-id="5061a-419">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-420">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-421">'Blazor'</span></span>
- <span data-ttu-id="5061a-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-422">'Identity'</span></span>
- <span data-ttu-id="5061a-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-424">'Razor'</span></span>
- <span data-ttu-id="5061a-425">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-426">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-427">'Blazor'</span></span>
- <span data-ttu-id="5061a-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-428">'Identity'</span></span>
- <span data-ttu-id="5061a-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-430">'Razor'</span></span>
- <span data-ttu-id="5061a-431">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-432">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-433">'Blazor'</span></span>
- <span data-ttu-id="5061a-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-434">'Identity'</span></span>
- <span data-ttu-id="5061a-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-436">'Razor'</span></span>
- <span data-ttu-id="5061a-437">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-438">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-439">'Blazor'</span></span>
- <span data-ttu-id="5061a-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-440">'Identity'</span></span>
- <span data-ttu-id="5061a-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-442">'Razor'</span></span>
- <span data-ttu-id="5061a-443">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-443">'SignalR' uid:</span></span> 

<span data-ttu-id="5061a-444">---------------------------------------- | :---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-445">'Blazor'</span></span>
- <span data-ttu-id="5061a-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-446">'Identity'</span></span>
- <span data-ttu-id="5061a-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-448">'Razor'</span></span>
- <span data-ttu-id="5061a-449">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-450">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-451">'Blazor'</span></span>
- <span data-ttu-id="5061a-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-452">'Identity'</span></span>
- <span data-ttu-id="5061a-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-454">'Razor'</span></span>
- <span data-ttu-id="5061a-455">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-456">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-457">'Blazor'</span></span>
- <span data-ttu-id="5061a-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-458">'Identity'</span></span>
- <span data-ttu-id="5061a-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-460">'Razor'</span></span>
- <span data-ttu-id="5061a-461">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-462">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-463">'Blazor'</span></span>
- <span data-ttu-id="5061a-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-464">'Identity'</span></span>
- <span data-ttu-id="5061a-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-466">'Razor'</span></span>
- <span data-ttu-id="5061a-467">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-468">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-469">'Blazor'</span></span>
- <span data-ttu-id="5061a-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-470">'Identity'</span></span>
- <span data-ttu-id="5061a-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-472">'Razor'</span></span>
- <span data-ttu-id="5061a-473">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-474">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-475">'Blazor'</span></span>
- <span data-ttu-id="5061a-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-476">'Identity'</span></span>
- <span data-ttu-id="5061a-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-478">'Razor'</span></span>
- <span data-ttu-id="5061a-479">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-480">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-481">'Blazor'</span></span>
- <span data-ttu-id="5061a-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-482">'Identity'</span></span>
- <span data-ttu-id="5061a-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-484">'Razor'</span></span>
- <span data-ttu-id="5061a-485">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-486">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-487">'Blazor'</span></span>
- <span data-ttu-id="5061a-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-488">'Identity'</span></span>
- <span data-ttu-id="5061a-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-490">'Razor'</span></span>
- <span data-ttu-id="5061a-491">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-492">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-493">'Blazor'</span></span>
- <span data-ttu-id="5061a-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-494">'Identity'</span></span>
- <span data-ttu-id="5061a-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-496">'Razor'</span></span>
- <span data-ttu-id="5061a-497">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-498">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-499">'Blazor'</span></span>
- <span data-ttu-id="5061a-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-500">'Identity'</span></span>
- <span data-ttu-id="5061a-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-502">'Razor'</span></span>
- <span data-ttu-id="5061a-503">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-504">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-505">'Blazor'</span></span>
- <span data-ttu-id="5061a-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-506">'Identity'</span></span>
- <span data-ttu-id="5061a-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-508">'Razor'</span></span>
- <span data-ttu-id="5061a-509">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-510">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-511">'Blazor'</span></span>
- <span data-ttu-id="5061a-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-512">'Identity'</span></span>
- <span data-ttu-id="5061a-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-514">'Razor'</span></span>
- <span data-ttu-id="5061a-515">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-516">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-517">'Blazor'</span></span>
- <span data-ttu-id="5061a-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-518">'Identity'</span></span>
- <span data-ttu-id="5061a-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-520">'Razor'</span></span>
- <span data-ttu-id="5061a-521">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-521">'SignalR' uid:</span></span> 

<span data-ttu-id="5061a-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Nie | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Tak | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Tak | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Tak | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Tak | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Nie | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Tak | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Tak |</span><span class="sxs-lookup"><span data-stu-id="5061a-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="5061a-523">Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub `DateTime` ), zawsze używają niezmiennej kultury.</span><span class="sxs-lookup"><span data-stu-id="5061a-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="5061a-524">W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.</span><span class="sxs-lookup"><span data-stu-id="5061a-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="5061a-525">Routing z adresami URL zawierającymi kropki</span><span class="sxs-lookup"><span data-stu-id="5061a-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="5061a-526">W obszarze Blazor aplikacje serwera domyślna trasa w *_Host. cshtml* to `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="5061a-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="5061a-527">Adres URL żądania, który zawiera kropkę ( `.` ) nie pasuje do trasy domyślnej, ponieważ adres URL wygląda na żądanie pliku.</span><span class="sxs-lookup"><span data-stu-id="5061a-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="5061a-528">BlazorAplikacja zwraca *404 — nie odnaleziono* odpowiedzi dla pliku statycznego, który nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="5061a-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="5061a-529">Aby użyć tras zawierających kropkę, skonfiguruj *_Host. cshtml* przy użyciu następującego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="5061a-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="5061a-530">`"/{**path}"`Szablon zawiera:</span><span class="sxs-lookup"><span data-stu-id="5061a-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="5061a-531">Podwójna gwiazdka *catch-all* ( `**` ) do przechwytywania ścieżki między wieloma granicami folderów bez kodowania ukośników ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="5061a-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="5061a-532">`path`Nazwa parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="5061a-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="5061a-533">Składnia *catch-all* parametru ( `*` / `**` ) **nie** jest obsługiwana w Razor składnikach (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="5061a-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="5061a-534">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="5061a-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="5061a-535">Składnik NavLink</span><span class="sxs-lookup"><span data-stu-id="5061a-535">NavLink component</span></span>

<span data-ttu-id="5061a-536">`NavLink`Podczas tworzenia linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML ( `<a>` ).</span><span class="sxs-lookup"><span data-stu-id="5061a-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="5061a-537">`NavLink`Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia `active` klasy CSS w zależności od tego, czy jest on `href` zgodny z bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="5061a-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="5061a-538">`active`Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5061a-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="5061a-539">Poniższy `NavMenu` składnik tworzy pasek nawigacyjny [Bootstrap](https://getbootstrap.com/docs/) , który pokazuje, jak używać `NavLink` składników:</span><span class="sxs-lookup"><span data-stu-id="5061a-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="5061a-540">Dostępne są dwie `NavLinkMatch` Opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="5061a-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="5061a-541">`NavLinkMatch.All`&ndash; `NavLink` Jest aktywny, gdy jest zgodny z całym bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="5061a-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="5061a-542">`NavLinkMatch.Prefix`(*ustawienie domyślne*) &ndash; `NavLink`Jest aktywny, gdy pasuje do dowolnego prefiksu bieżącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="5061a-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="5061a-543">W poprzednim przykładzie Strona główna `NavLink` `href=""` odpowiada GŁÓWNEmu adresowi URL i odbiera tylko `active` klasy CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="5061a-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="5061a-544">Druga `NavLink` otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `MyComponent` prefiksem (na przykład `https://localhost:5001/MyComponent` i `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="5061a-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="5061a-545">Dodatkowe `NavLink` atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="5061a-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="5061a-546">W poniższym przykładzie `NavLink` składnik zawiera `target` atrybut:</span><span class="sxs-lookup"><span data-stu-id="5061a-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="5061a-547">Renderuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="5061a-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="5061a-548">Pomoc dotycząca stanu identyfikatora URI i nawigacji</span><span class="sxs-lookup"><span data-stu-id="5061a-548">URI and navigation state helpers</span></span>

<span data-ttu-id="5061a-549">Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacją w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="5061a-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="5061a-550">`NavigationManager`zawiera zdarzenie i metody przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="5061a-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="5061a-551">Członek</span><span class="sxs-lookup"><span data-stu-id="5061a-551">Member</span></span> | <span data-ttu-id="5061a-552">Opis</span><span class="sxs-lookup"><span data-stu-id="5061a-552">Description</span></span> |
| ---
<span data-ttu-id="5061a-553">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-554">'Blazor'</span></span>
- <span data-ttu-id="5061a-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-555">'Identity'</span></span>
- <span data-ttu-id="5061a-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-557">'Razor'</span></span>
- <span data-ttu-id="5061a-558">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-558">'SignalR' uid:</span></span> 

<span data-ttu-id="5061a-559">--- | ---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-560">'Blazor'</span></span>
- <span data-ttu-id="5061a-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-561">'Identity'</span></span>
- <span data-ttu-id="5061a-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-563">'Razor'</span></span>
- <span data-ttu-id="5061a-564">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-565">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-566">'Blazor'</span></span>
- <span data-ttu-id="5061a-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-567">'Identity'</span></span>
- <span data-ttu-id="5061a-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-569">'Razor'</span></span>
- <span data-ttu-id="5061a-570">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5061a-571">title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5061a-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5061a-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5061a-572">'Blazor'</span></span>
- <span data-ttu-id="5061a-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5061a-573">'Identity'</span></span>
- <span data-ttu-id="5061a-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5061a-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="5061a-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5061a-575">'Razor'</span></span>
- <span data-ttu-id="5061a-576">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5061a-576">'SignalR' uid:</span></span> 

<span data-ttu-id="5061a-577">------ | | Identyfikator URI | Pobiera bieżący bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="5061a-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="5061a-578">| | BaseUri | Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="5061a-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="5061a-579">Zazwyczaj `BaseUri` odpowiada `href` atrybutowi `<base>` elementu dokumentu w *wwwroot/index.html* ( Blazor webassembly) lub *Pages/_Host. cshtml* ( Blazor serwer).</span><span class="sxs-lookup"><span data-stu-id="5061a-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="5061a-580">| | Typu NavigateTo | Przechodzi do określonego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="5061a-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="5061a-581">Jeśli `forceLoad` jest `true` :</span><span class="sxs-lookup"><span data-stu-id="5061a-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="5061a-582">Routing po stronie klienta jest pomijany.</span><span class="sxs-lookup"><span data-stu-id="5061a-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="5061a-583">W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="5061a-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="5061a-584">| | LocationChanged | Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5061a-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="5061a-585">| | ToAbsoluteUri | Konwertuje względny identyfikator URI na bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="5061a-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="5061a-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej przez `GetBaseUri` ), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI.</span><span class="sxs-lookup"><span data-stu-id="5061a-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="5061a-587">Poniższy składnik przechodzi do `Counter` składnika aplikacji po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="5061a-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="5061a-588">Poniższy składnik obsługuje zdarzenie zmiany lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="5061a-588">The following component handles a location changed event.</span></span> <span data-ttu-id="5061a-589">Metoda jest odłączana, `HandleLocationChanged` gdy `Dispose` jest wywoływana przez platformę.</span><span class="sxs-lookup"><span data-stu-id="5061a-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="5061a-590">Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.</span><span class="sxs-lookup"><span data-stu-id="5061a-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="5061a-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>zawiera następujące informacje o zdarzeniu:</span><span class="sxs-lookup"><span data-stu-id="5061a-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="5061a-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;Adres URL nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="5061a-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="5061a-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Jeśli `true` Blazor przechwycono nawigację z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="5061a-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="5061a-594">Jeśli `false` , element [nawigacyjny. typu NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) spowodował wystąpienie nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5061a-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="5061a-595">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="5061a-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
