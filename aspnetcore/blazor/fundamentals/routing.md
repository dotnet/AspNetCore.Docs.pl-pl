---
title: BlazorRouting ASP.NET Core
author: guardrex
description: Dowiedz się, jak kierować żądania w aplikacjach i informacje o składniku NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: 09e7ca9c03103de116c566352496174e97fbc3ce
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90593011"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="00f1f-103">BlazorRouting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00f1f-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="00f1f-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="00f1f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="00f1f-105">Dowiedz się, jak kierować żądania i jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnika do tworzenia linków nawigacji w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="00f1f-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="00f1f-106">ASP.NET Core integracja z routingiem punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="00f1f-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="00f1f-107">Blazor Server Program jest zintegrowany z [ASP.NET Core routingiem punktów końcowych](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="00f1f-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="00f1f-108">Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za pomocą <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> programu w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="00f1f-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="00f1f-109">Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do Razor strony, które pełnią rolę hosta dla części aplikacji po stronie serwera Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="00f1f-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="00f1f-110">Zgodnie z Konwencją strona *hosta* ma zwykle nazwę `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="00f1f-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="00f1f-111">Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras.</span><span class="sxs-lookup"><span data-stu-id="00f1f-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="00f1f-112">Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="00f1f-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="00f1f-113">Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania działania Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00f1f-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="00f1f-114">Aby uzyskać informacje na temat konfigurowania <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> dla hosta niebędącego głównym adresem URL, zobacz <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="00f1f-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="00f1f-115">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="00f1f-115">Route templates</span></span>

<span data-ttu-id="00f1f-116"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia kierowanie do każdego składnika z określoną trasą.</span><span class="sxs-lookup"><span data-stu-id="00f1f-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="00f1f-117"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik pojawi się w `App.razor` pliku:</span><span class="sxs-lookup"><span data-stu-id="00f1f-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="00f1f-118">Gdy `.razor` plik z `@page` dyrektywą jest kompilowany, wygenerowana Klasa jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="00f1f-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="00f1f-119">W środowisku uruchomieniowym <xref:Microsoft.AspNetCore.Components.RouteView> składnik:</span><span class="sxs-lookup"><span data-stu-id="00f1f-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="00f1f-120">Odbiera <xref:Microsoft.AspNetCore.Components.RouteData> od siebie <xref:Microsoft.AspNetCore.Components.Routing.Router> wraz z dowolnymi żądanymi parametrami.</span><span class="sxs-lookup"><span data-stu-id="00f1f-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="00f1f-121">Renderuje określony składnik za pomocą układu (lub opcjonalnego układu domyślnego) przy użyciu określonych parametrów.</span><span class="sxs-lookup"><span data-stu-id="00f1f-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="00f1f-122">Opcjonalnie można określić <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr z klasą układu, która ma być używana dla składników, które nie określają układu.</span><span class="sxs-lookup"><span data-stu-id="00f1f-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="00f1f-123">Domyślne Blazor Szablony określają `MainLayout` składnik.</span><span class="sxs-lookup"><span data-stu-id="00f1f-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="00f1f-124">`MainLayout.razor` znajduje się w folderze szablonu projektu `Shared` .</span><span class="sxs-lookup"><span data-stu-id="00f1f-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="00f1f-125">Aby uzyskać więcej informacji na temat układów, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="00f1f-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="00f1f-126">Do składnika można zastosować wiele szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="00f1f-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="00f1f-127">Poniższy składnik odpowiada na żądania dla `/BlazorRoute` i `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="00f1f-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="00f1f-128">Aby adresy URL zostały poprawnie rozpoznane, aplikacja musi zawierać `<base>` tag w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ) z ścieżką bazową aplikacji określoną w `href` atrybucie ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="00f1f-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="00f1f-129">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="00f1f-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="00f1f-130">Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości</span><span class="sxs-lookup"><span data-stu-id="00f1f-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="00f1f-131"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.</span><span class="sxs-lookup"><span data-stu-id="00f1f-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="00f1f-132">W `App.razor` pliku Ustaw zawartość niestandardową w <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametrze szablonu <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika:</span><span class="sxs-lookup"><span data-stu-id="00f1f-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="00f1f-133">Zawartość `<NotFound>` tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="00f1f-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="00f1f-134">Aby zastosować domyślny układ do <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> zawartości, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="00f1f-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="00f1f-135">Kierowanie do składników z wielu zestawów</span><span class="sxs-lookup"><span data-stu-id="00f1f-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="00f1f-136">Użyj <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametru, aby określić dodatkowe zestawy dla <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika do uwzględnienia podczas wyszukiwania składników routingu.</span><span class="sxs-lookup"><span data-stu-id="00f1f-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="00f1f-137">Określone zestawy są traktowane jako uzupełnienie `AppAssembly` określonego zestawu.</span><span class="sxs-lookup"><span data-stu-id="00f1f-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="00f1f-138">W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w bibliotece klas, do której się odwołuje.</span><span class="sxs-lookup"><span data-stu-id="00f1f-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="00f1f-139">W poniższym <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> przykładzie przedstawiono obsługę routingu dla `Component1` :</span><span class="sxs-lookup"><span data-stu-id="00f1f-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="00f1f-140">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="00f1f-140">Route parameters</span></span>

<span data-ttu-id="00f1f-141">Router używa parametrów trasy do wypełniania odpowiednich parametrów składnika o tej samej nazwie (bez uwzględniania wielkości liter):</span><span class="sxs-lookup"><span data-stu-id="00f1f-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="00f1f-142">Parametry opcjonalne nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="00f1f-142">Optional parameters aren't supported.</span></span> <span data-ttu-id="00f1f-143">`@page`W poprzednim przykładzie są stosowane dwie dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="00f1f-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="00f1f-144">Pierwszy zezwala na nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="00f1f-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="00f1f-145">Druga `@page` dyrektywa przyjmuje `{text}` parametr Route i przypisuje wartość do `Text` właściwości.</span><span class="sxs-lookup"><span data-stu-id="00f1f-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="00f1f-146">Ograniczenia trasy</span><span class="sxs-lookup"><span data-stu-id="00f1f-146">Route constraints</span></span>

<span data-ttu-id="00f1f-147">Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.</span><span class="sxs-lookup"><span data-stu-id="00f1f-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="00f1f-148">W poniższym przykładzie trasa do `Users` składnika dopasowuje się tylko wtedy, gdy:</span><span class="sxs-lookup"><span data-stu-id="00f1f-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="00f1f-149">`Id`Segment trasy jest obecny w adresie URL żądania.</span><span class="sxs-lookup"><span data-stu-id="00f1f-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="00f1f-150">`Id`Segment jest liczbą całkowitą ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="00f1f-150">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="00f1f-151">Dostępne są ograniczenia trasy podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="00f1f-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="00f1f-152">W przypadku ograniczeń trasy, które pasują do niezmiennej kultury, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="00f1f-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="00f1f-153">Typu</span><span class="sxs-lookup"><span data-stu-id="00f1f-153">Constraint</span></span> | <span data-ttu-id="00f1f-154">Przykład</span><span class="sxs-lookup"><span data-stu-id="00f1f-154">Example</span></span>           | <span data-ttu-id="00f1f-155">Przykładowe dopasowania</span><span class="sxs-lookup"><span data-stu-id="00f1f-155">Example Matches</span></span>                                                                  | <span data-ttu-id="00f1f-156">Niezmiennej</span><span class="sxs-lookup"><span data-stu-id="00f1f-156">Invariant</span></span><br><span data-ttu-id="00f1f-157">kultura</span><span class="sxs-lookup"><span data-stu-id="00f1f-157">culture</span></span><br><span data-ttu-id="00f1f-158">parowanie</span><span class="sxs-lookup"><span data-stu-id="00f1f-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="00f1f-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="00f1f-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="00f1f-160">Nie</span><span class="sxs-lookup"><span data-stu-id="00f1f-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="00f1f-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="00f1f-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="00f1f-162">Yes</span><span class="sxs-lookup"><span data-stu-id="00f1f-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="00f1f-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="00f1f-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="00f1f-164">Yes</span><span class="sxs-lookup"><span data-stu-id="00f1f-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="00f1f-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="00f1f-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="00f1f-166">Yes</span><span class="sxs-lookup"><span data-stu-id="00f1f-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="00f1f-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="00f1f-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="00f1f-168">Yes</span><span class="sxs-lookup"><span data-stu-id="00f1f-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="00f1f-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="00f1f-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="00f1f-170">Nie</span><span class="sxs-lookup"><span data-stu-id="00f1f-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="00f1f-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="00f1f-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="00f1f-172">Yes</span><span class="sxs-lookup"><span data-stu-id="00f1f-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="00f1f-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="00f1f-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="00f1f-174">Yes</span><span class="sxs-lookup"><span data-stu-id="00f1f-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="00f1f-175">Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub <xref:System.DateTime> ), zawsze używają niezmiennej kultury.</span><span class="sxs-lookup"><span data-stu-id="00f1f-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="00f1f-176">W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.</span><span class="sxs-lookup"><span data-stu-id="00f1f-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="00f1f-177">Routing z adresami URL zawierającymi kropki</span><span class="sxs-lookup"><span data-stu-id="00f1f-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="00f1f-178">W przypadku hostowanych Blazor WebAssembly i Blazor Server aplikacji szablon trasy domyślnej po stronie serwera zakłada, że jeśli ostatni segment adresu URL żądania zawiera kropkę ( `.` ), w której zażądano pliku (na przykład `https://localhost.com:5001/example/some.thing` ).</span><span class="sxs-lookup"><span data-stu-id="00f1f-178">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="00f1f-179">Bez dodatkowej konfiguracji aplikacja zwraca odpowiedź *404 — nie można znaleźć* , jeśli było to przeznaczone do kierowania do składnika.</span><span class="sxs-lookup"><span data-stu-id="00f1f-179">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="00f1f-180">Aby użyć trasy z co najmniej jednym parametrem zawierającym kropkę, aplikacja musi skonfigurować trasę z szablonem niestandardowym.</span><span class="sxs-lookup"><span data-stu-id="00f1f-180">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="00f1f-181">Rozważmy następujący `Example` składnik, który może odebrać parametr trasy z ostatniego segmentu adresu URL:</span><span class="sxs-lookup"><span data-stu-id="00f1f-181">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="00f1f-182">Aby zezwolić aplikacji *serwera* hostowanego Blazor WebAssembly rozwiązania na kierowanie żądania z kropką w `param` parametrze, Dodaj szablon alternatywnej trasy pliku z opcjonalnym parametrem w `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="00f1f-182">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="00f1f-183">Aby skonfigurować Blazor Server aplikację do kierowania żądania z kropką w `param` parametrze, Dodaj szablon trasy strony rezerwowej z opcjonalnym parametrem w `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="00f1f-183">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="00f1f-184">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="00f1f-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="00f1f-185">Catch-wszystkie parametry tras</span><span class="sxs-lookup"><span data-stu-id="00f1f-185">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="00f1f-186">*Ta sekcja ma zastosowanie do ASP.NET Core w programie .NET 5 Release Candidate 1 (RC1) lub nowszym.*</span><span class="sxs-lookup"><span data-stu-id="00f1f-186">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="00f1f-187">Wszystkie parametry trasy, które przechwytują ścieżki między wieloma granicami folderów, są obsługiwane w składnikach.</span><span class="sxs-lookup"><span data-stu-id="00f1f-187">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="00f1f-188">Wartość parametru catch-all musi być:</span><span class="sxs-lookup"><span data-stu-id="00f1f-188">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="00f1f-189">Nazwa jest zgodna z nazwą segmentu trasy.</span><span class="sxs-lookup"><span data-stu-id="00f1f-189">Named to match the route segment name.</span></span> <span data-ttu-id="00f1f-190">W nazewnictwie nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="00f1f-190">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="00f1f-191">`string`Typ.</span><span class="sxs-lookup"><span data-stu-id="00f1f-191">A `string` type.</span></span> <span data-ttu-id="00f1f-192">Struktura nie zapewnia automatycznego rzutowania.</span><span class="sxs-lookup"><span data-stu-id="00f1f-192">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="00f1f-193">Na końcu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="00f1f-193">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="00f1f-194">Dla adresu URL `/page/this/is/a/test` z szablonem trasy dla `/page/{*pageRoute}` , wartość `PageRoute` jest ustawiona na `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="00f1f-194">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="00f1f-195">Ukośniki i segmenty ścieżki przechwyconej są zdekodowane.</span><span class="sxs-lookup"><span data-stu-id="00f1f-195">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="00f1f-196">Dla szablonu trasy w programie `/page/{*pageRoute}` adres URL `/page/this/is/a%2Ftest%2A` daje wartość `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="00f1f-196">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="00f1f-197">Wszystkie parametry tras są obsługiwane w ASP.NET Core w programie .NET 5 Release Candidate 1 (RC1) lub nowszym. \*</span><span class="sxs-lookup"><span data-stu-id="00f1f-197">Catch-all route parameters are supported in ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.\*</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="00f1f-198">Składnik NavLink</span><span class="sxs-lookup"><span data-stu-id="00f1f-198">NavLink component</span></span>

<span data-ttu-id="00f1f-199"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Podczas tworzenia linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML ( `<a>` ).</span><span class="sxs-lookup"><span data-stu-id="00f1f-199">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="00f1f-200"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia `active` klasy CSS w zależności od tego, czy jest on `href` zgodny z bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="00f1f-200">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="00f1f-201">`active`Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="00f1f-201">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="00f1f-202">Opcjonalnie Przypisz nazwę klasy CSS do, aby <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> zastosować niestandardową klasę CSS do renderowanego łącza, gdy bieżąca trasa jest zgodna z `href` .</span><span class="sxs-lookup"><span data-stu-id="00f1f-202">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="00f1f-203">Poniższy `NavMenu` składnik tworzy [`Bootstrap`](https://getbootstrap.com/docs/) pasek nawigacyjny, który pokazuje, jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składników:</span><span class="sxs-lookup"><span data-stu-id="00f1f-203">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="00f1f-204">Dostępne są dwie <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="00f1f-204">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="00f1f-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Jest aktywny, gdy jest zgodny z całym bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="00f1f-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="00f1f-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*ustawienie domyślne*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> jest aktywny, gdy pasuje do dowolnego PREFIKSU bieżącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="00f1f-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="00f1f-207">W poprzednim przykładzie Strona główna <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` odpowiada GŁÓWNEmu adresowi URL i odbiera tylko `active` klasy CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="00f1f-207">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="00f1f-208">Druga <xref:Microsoft.AspNetCore.Components.Routing.NavLink> otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `MyComponent` prefiksem (na przykład `https://localhost:5001/MyComponent` i `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="00f1f-208">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="00f1f-209">Dodatkowe <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="00f1f-209">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="00f1f-210">W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnik zawiera `target` atrybut:</span><span class="sxs-lookup"><span data-stu-id="00f1f-210">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="00f1f-211">Renderuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="00f1f-211">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="00f1f-212">Ze względu na sposób, który Blazor renderuje zawartość podrzędną, `NavLink` składniki renderowania wewnątrz `for` pętli wymagają zmiennej lokalnego indeksu, jeśli zmienna pętli zwiększania jest używana w `NavLink` zawartości składnika (potomne):</span><span class="sxs-lookup"><span data-stu-id="00f1f-212">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="00f1f-213">Użycie zmiennej index w tym scenariuszu jest wymaganiem dla **dowolnego** składnika podrzędnego, który używa zmiennej pętli w jej [zawartości podrzędnej](xref:blazor/components/index#child-content), a nie tylko `NavLink` składnika.</span><span class="sxs-lookup"><span data-stu-id="00f1f-213">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="00f1f-214">Alternatywnie możesz użyć `foreach` pętli z <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="00f1f-214">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="00f1f-215">Pomoc dotycząca stanu identyfikatora URI i nawigacji</span><span class="sxs-lookup"><span data-stu-id="00f1f-215">URI and navigation state helpers</span></span>

<span data-ttu-id="00f1f-216">Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacją w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="00f1f-216">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="00f1f-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> zawiera zdarzenie i metody przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="00f1f-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="00f1f-218">Członek</span><span class="sxs-lookup"><span data-stu-id="00f1f-218">Member</span></span> | <span data-ttu-id="00f1f-219">Opis</span><span class="sxs-lookup"><span data-stu-id="00f1f-219">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="00f1f-220">Pobiera bieżący bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="00f1f-220">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="00f1f-221">Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="00f1f-221">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="00f1f-222">Zazwyczaj <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpowiada `href` atrybutowi `<base>` elementu dokumentu w `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="00f1f-222">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="00f1f-223">Przechodzi do określonego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="00f1f-223">Navigates to the specified URI.</span></span> <span data-ttu-id="00f1f-224">Jeśli `forceLoad` jest `true` :</span><span class="sxs-lookup"><span data-stu-id="00f1f-224">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="00f1f-225">Routing po stronie klienta jest pomijany.</span><span class="sxs-lookup"><span data-stu-id="00f1f-225">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="00f1f-226">W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="00f1f-226">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="00f1f-227">Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji.</span><span class="sxs-lookup"><span data-stu-id="00f1f-227">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="00f1f-228">Konwertuje względny identyfikator URI na bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="00f1f-228">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="00f1f-229">Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej przez <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI.</span><span class="sxs-lookup"><span data-stu-id="00f1f-229">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="00f1f-230">Poniższy składnik przechodzi do `Counter` składnika aplikacji po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="00f1f-230">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="00f1f-231">Poniższy składnik obsługuje zdarzenie zmiany lokalizacji przez subskrybowanie do <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="00f1f-231">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="00f1f-232">Metoda jest odłączana, `HandleLocationChanged` gdy `Dispose` jest wywoływana przez platformę.</span><span class="sxs-lookup"><span data-stu-id="00f1f-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="00f1f-233">Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.</span><span class="sxs-lookup"><span data-stu-id="00f1f-233">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="00f1f-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> zawiera następujące informacje o zdarzeniu:</span><span class="sxs-lookup"><span data-stu-id="00f1f-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="00f1f-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adres URL nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="00f1f-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="00f1f-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Jeśli `true` Blazor przechwytuje nawigację z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="00f1f-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="00f1f-237">Jeśli `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wystąpiła nawigacja.</span><span class="sxs-lookup"><span data-stu-id="00f1f-237">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="00f1f-238">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="00f1f-238">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="00f1f-239">Ciąg zapytania i parametry analizy</span><span class="sxs-lookup"><span data-stu-id="00f1f-239">Query string and parse parameters</span></span>

<span data-ttu-id="00f1f-240">Ciąg zapytania żądania można uzyskać z <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="00f1f-240">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="00f1f-241">Aby przeanalizować parametry ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="00f1f-241">To parse a query string's parameters:</span></span>

* <span data-ttu-id="00f1f-242">Dodaj odwołanie do pakietu dla [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="00f1f-242">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="00f1f-243">Uzyskaj wartość po przeanalizowaniu ciągu zapytania za pomocą elementu <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="00f1f-243">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="00f1f-244">Symbol zastępczy `{KEY}` w powyższym przykładzie jest kluczem parametrów ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="00f1f-244">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="00f1f-245">Na przykład para klucz adresu URL `?ship=Tardis` używa klucza `ship` .</span><span class="sxs-lookup"><span data-stu-id="00f1f-245">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
