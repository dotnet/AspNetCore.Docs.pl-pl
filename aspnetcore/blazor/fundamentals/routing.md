---
title: :::no-loc(Blazor):::Routing ASP.NET Core
author: guardrex
description: Dowiedz się, jak kierować żądania w aplikacjach i informacje o składniku NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 585b697aedf31bce2305df0ec5f84824c4019156
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637694"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="c1c3f-103">:::no-loc(Blazor):::Routing ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1c3f-103">ASP.NET Core :::no-loc(Blazor)::: routing</span></span>

<span data-ttu-id="c1c3f-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1c3f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c1c3f-105">Dowiedz się, jak kierować żądania i jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnika do tworzenia linków nawigacji w :::no-loc(Blazor)::: aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in :::no-loc(Blazor)::: apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="c1c3f-106">ASP.NET Core integracja z routingiem punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="c1c3f-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="c1c3f-107">:::no-loc(Blazor Server)::: Program jest zintegrowany z [ASP.NET Core routingiem punktów końcowych](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-107">:::no-loc(Blazor Server)::: is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="c1c3f-108">Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za pomocą <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A> programu w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c1c3f-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="c1c3f-109">Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do :::no-loc(Razor)::: strony, które pełnią rolę hosta dla części aplikacji po stronie serwera :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-109">The most typical configuration is to route all requests to a :::no-loc(Razor)::: page, which acts as the host for the server-side part of the :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="c1c3f-110">Zgodnie z Konwencją strona *hosta* ma zwykle nazwę `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="c1c3f-111">Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="c1c3f-112">Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="c1c3f-113">Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania działania :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-113">This allows the app to use others controllers and pages without interfering with the :::no-loc(Blazor Server)::: app.</span></span>

<span data-ttu-id="c1c3f-114">Aby uzyskać informacje na temat konfigurowania <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> dla hosta niebędącego głównym adresem URL, zobacz <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="c1c3f-115">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="c1c3f-115">Route templates</span></span>

<span data-ttu-id="c1c3f-116"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia kierowanie do każdego składnika z określoną trasą.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="c1c3f-117"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik pojawi się w `App.razor` pliku:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

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

<span data-ttu-id="c1c3f-118">Gdy `.razor` plik z `@page` dyrektywą jest kompilowany, wygenerowana Klasa jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="c1c3f-119">W środowisku uruchomieniowym <xref:Microsoft.AspNetCore.Components.RouteView> składnik:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="c1c3f-120">Odbiera <xref:Microsoft.AspNetCore.Components.RouteData> od siebie <xref:Microsoft.AspNetCore.Components.Routing.Router> wraz z dowolnymi żądanymi parametrami.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="c1c3f-121">Renderuje określony składnik za pomocą układu (lub opcjonalnego układu domyślnego) przy użyciu określonych parametrów.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="c1c3f-122">Opcjonalnie można określić <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr z klasą układu, która ma być używana dla składników, które nie określają układu.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="c1c3f-123">Domyślne :::no-loc(Blazor)::: Szablony określają `MainLayout` składnik.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-123">The default :::no-loc(Blazor)::: templates specify the `MainLayout` component.</span></span> <span data-ttu-id="c1c3f-124">`MainLayout.razor` znajduje się w folderze szablonu projektu `Shared` .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="c1c3f-125">Aby uzyskać więcej informacji na temat układów, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="c1c3f-126">Do składnika można zastosować wiele szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="c1c3f-127">Poniższy składnik odpowiada na żądania dla `/:::no-loc(Blazor):::Route` i `/Different:::no-loc(Blazor):::Route` :</span><span class="sxs-lookup"><span data-stu-id="c1c3f-127">The following component responds to requests for `/:::no-loc(Blazor):::Route` and `/Different:::no-loc(Blazor):::Route`:</span></span>

```razor
@page "/:::no-loc(Blazor):::Route"
@page "/Different:::no-loc(Blazor):::Route"

<h1>:::no-loc(Blazor)::: routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="c1c3f-128">Aby adresy URL zostały poprawnie rozpoznane, aplikacja musi zawierać `<base>` tag w `wwwroot/index.html` pliku ( :::no-loc(Blazor WebAssembly)::: ) lub `Pages/_Host.cshtml` pliku ( :::no-loc(Blazor Server)::: ) z ścieżką bazową aplikacji określoną w `href` atrybucie ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="c1c3f-129">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="c1c3f-130">Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości</span><span class="sxs-lookup"><span data-stu-id="c1c3f-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="c1c3f-131"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="c1c3f-132">W `App.razor` pliku Ustaw zawartość niestandardową w <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametrze szablonu <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="c1c3f-133">Zawartość `<NotFound>` tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="c1c3f-134">Aby zastosować domyślny układ do <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> zawartości, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="c1c3f-135">Kierowanie do składników z wielu zestawów</span><span class="sxs-lookup"><span data-stu-id="c1c3f-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="c1c3f-136">Użyj <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametru, aby określić dodatkowe zestawy dla <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika do uwzględnienia podczas wyszukiwania składników routingu.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="c1c3f-137">Określone zestawy są traktowane jako uzupełnienie `AppAssembly` określonego zestawu.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="c1c3f-138">W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w bibliotece klas, do której się odwołuje.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="c1c3f-139">W poniższym <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> przykładzie przedstawiono obsługę routingu dla `Component1` :</span><span class="sxs-lookup"><span data-stu-id="c1c3f-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="c1c3f-140">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="c1c3f-140">Route parameters</span></span>

<span data-ttu-id="c1c3f-141">Router używa parametrów trasy do wypełniania odpowiednich parametrów składnika o tej samej nazwie (bez uwzględniania wielkości liter).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c1c3f-142">Parametry opcjonalne są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-142">Optional parameters are supported.</span></span> <span data-ttu-id="c1c3f-143">W poniższym przykładzie `text` opcjonalny parametr przypisuje wartość segmentu trasy do `Text` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-143">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="c1c3f-144">Jeśli segment nie istnieje, wartość `Text` jest ustawiona na `fantastic` :</span><span class="sxs-lookup"><span data-stu-id="c1c3f-144">If the segment isn't present, the value of `Text` is set to `fantastic`:</span></span>

```razor
@page "/RouteParameter/{text?}"

<h1>:::no-loc(Blazor)::: is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>:::no-loc(Blazor)::: is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="c1c3f-145">Parametry opcjonalne nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="c1c3f-146">`@page`W poprzednim przykładzie są stosowane dwie dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="c1c3f-147">Pierwszy zezwala na nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c1c3f-148">Druga `@page` dyrektywa przyjmuje `{text}` parametr Route i przypisuje wartość do `Text` właściwości.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

## <a name="route-constraints"></a><span data-ttu-id="c1c3f-149">Ograniczenia trasy</span><span class="sxs-lookup"><span data-stu-id="c1c3f-149">Route constraints</span></span>

<span data-ttu-id="c1c3f-150">Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="c1c3f-151">W poniższym przykładzie trasa do `Users` składnika dopasowuje się tylko wtedy, gdy:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="c1c3f-152">`Id`Segment trasy jest obecny w adresie URL żądania.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="c1c3f-153">`Id`Segment jest liczbą całkowitą ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="c1c3f-154">Dostępne są ograniczenia trasy podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="c1c3f-155">W przypadku ograniczeń trasy, które pasują do niezmiennej kultury, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="c1c3f-156">Typu</span><span class="sxs-lookup"><span data-stu-id="c1c3f-156">Constraint</span></span> | <span data-ttu-id="c1c3f-157">Przykład</span><span class="sxs-lookup"><span data-stu-id="c1c3f-157">Example</span></span>           | <span data-ttu-id="c1c3f-158">Przykładowe dopasowania</span><span class="sxs-lookup"><span data-stu-id="c1c3f-158">Example Matches</span></span>                                                                  | <span data-ttu-id="c1c3f-159">Niezmiennej</span><span class="sxs-lookup"><span data-stu-id="c1c3f-159">Invariant</span></span><br><span data-ttu-id="c1c3f-160">kultura</span><span class="sxs-lookup"><span data-stu-id="c1c3f-160">culture</span></span><br><span data-ttu-id="c1c3f-161">parowanie</span><span class="sxs-lookup"><span data-stu-id="c1c3f-161">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="c1c3f-162">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="c1c3f-162">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="c1c3f-163">Nie</span><span class="sxs-lookup"><span data-stu-id="c1c3f-163">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="c1c3f-164">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="c1c3f-164">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="c1c3f-165">Tak</span><span class="sxs-lookup"><span data-stu-id="c1c3f-165">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="c1c3f-166">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="c1c3f-166">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="c1c3f-167">Tak</span><span class="sxs-lookup"><span data-stu-id="c1c3f-167">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="c1c3f-168">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="c1c3f-168">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="c1c3f-169">Tak</span><span class="sxs-lookup"><span data-stu-id="c1c3f-169">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="c1c3f-170">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="c1c3f-170">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="c1c3f-171">Tak</span><span class="sxs-lookup"><span data-stu-id="c1c3f-171">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="c1c3f-172">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="c1c3f-172">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="c1c3f-173">Nie</span><span class="sxs-lookup"><span data-stu-id="c1c3f-173">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="c1c3f-174">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="c1c3f-174">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="c1c3f-175">Tak</span><span class="sxs-lookup"><span data-stu-id="c1c3f-175">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="c1c3f-176">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="c1c3f-176">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="c1c3f-177">Tak</span><span class="sxs-lookup"><span data-stu-id="c1c3f-177">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="c1c3f-178">Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub <xref:System.DateTime> ), zawsze używają niezmiennej kultury.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-178">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="c1c3f-179">W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-179">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="c1c3f-180">Routing z adresami URL zawierającymi kropki</span><span class="sxs-lookup"><span data-stu-id="c1c3f-180">Routing with URLs that contain dots</span></span>

<span data-ttu-id="c1c3f-181">W przypadku hostowanych :::no-loc(Blazor WebAssembly)::: i :::no-loc(Blazor Server)::: aplikacji szablon trasy domyślnej po stronie serwera zakłada, że jeśli ostatni segment adresu URL żądania zawiera kropkę ( `.` ), w której zażądano pliku (na przykład `https://localhost.com:5001/example/some.thing` ).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-181">For hosted :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server)::: apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="c1c3f-182">Bez dodatkowej konfiguracji aplikacja zwraca odpowiedź *404 — nie można znaleźć* , jeśli było to przeznaczone do kierowania do składnika.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-182">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="c1c3f-183">Aby użyć trasy z co najmniej jednym parametrem zawierającym kropkę, aplikacja musi skonfigurować trasę z szablonem niestandardowym.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-183">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="c1c3f-184">Rozważmy następujący `Example` składnik, który może odebrać parametr trasy z ostatniego segmentu adresu URL:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-184">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

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

<span data-ttu-id="c1c3f-185">Aby zezwolić aplikacji *serwera* hostowanego :::no-loc(Blazor WebAssembly)::: rozwiązania na kierowanie żądania z kropką w `param` parametrze, Dodaj szablon alternatywnej trasy pliku z opcjonalnym parametrem w `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="c1c3f-185">To permit the *Server* app of a hosted :::no-loc(Blazor WebAssembly)::: solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="c1c3f-186">Aby skonfigurować :::no-loc(Blazor Server)::: aplikację do kierowania żądania z kropką w `param` parametrze, Dodaj szablon trasy strony rezerwowej z opcjonalnym parametrem w `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="c1c3f-186">To configure a :::no-loc(Blazor Server)::: app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="c1c3f-187">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-187">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="c1c3f-188">Catch-wszystkie parametry tras</span><span class="sxs-lookup"><span data-stu-id="c1c3f-188">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c1c3f-189">*Ta sekcja ma zastosowanie do ASP.NET Core w programie .NET 5 Release Candidate 1 (RC1) lub nowszym.*</span><span class="sxs-lookup"><span data-stu-id="c1c3f-189">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="c1c3f-190">Wszystkie parametry trasy, które przechwytują ścieżki między wieloma granicami folderów, są obsługiwane w składnikach.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-190">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="c1c3f-191">Wartość parametru catch-all musi być:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-191">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="c1c3f-192">Nazwa jest zgodna z nazwą segmentu trasy.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-192">Named to match the route segment name.</span></span> <span data-ttu-id="c1c3f-193">W nazewnictwie nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-193">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="c1c3f-194">`string`Typ.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-194">A `string` type.</span></span> <span data-ttu-id="c1c3f-195">Struktura nie zapewnia automatycznego rzutowania.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-195">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="c1c3f-196">Na końcu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-196">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="c1c3f-197">Dla adresu URL `/page/this/is/a/test` z szablonem trasy dla `/page/{*pageRoute}` , wartość `PageRoute` jest ustawiona na `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-197">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="c1c3f-198">Ukośniki i segmenty ścieżki przechwyconej są zdekodowane.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-198">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="c1c3f-199">Dla szablonu trasy w programie `/page/{*pageRoute}` adres URL `/page/this/is/a%2Ftest%2A` daje wartość `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-199">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c1c3f-200">Wszystkie parametry tras są obsługiwane w ASP.NET Core 5,0 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-200">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="c1c3f-201">Składnik NavLink</span><span class="sxs-lookup"><span data-stu-id="c1c3f-201">NavLink component</span></span>

<span data-ttu-id="c1c3f-202"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Podczas tworzenia linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML ( `<a>` ).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-202">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="c1c3f-203"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia `active` klasy CSS w zależności od tego, czy jest on `href` zgodny z bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-203">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="c1c3f-204">`active`Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-204">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="c1c3f-205">Opcjonalnie Przypisz nazwę klasy CSS do, aby <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> zastosować niestandardową klasę CSS do renderowanego łącza, gdy bieżąca trasa jest zgodna z `href` .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-205">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="c1c3f-206">Poniższy `NavMenu` składnik tworzy [`Bootstrap`](https://getbootstrap.com/docs/) pasek nawigacyjny, który pokazuje, jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składników:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-206">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="c1c3f-207">Dostępne są dwie <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-207">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="c1c3f-208"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Jest aktywny, gdy jest zgodny z całym bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-208"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="c1c3f-209"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> ( *ustawienie domyślne* ): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> jest aktywny, gdy pasuje do dowolnego PREFIKSU bieżącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-209"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> ( *default* ): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="c1c3f-210">W poprzednim przykładzie Strona główna <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` odpowiada GŁÓWNEmu adresowi URL i odbiera tylko `active` klasy CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-210">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="c1c3f-211">Druga <xref:Microsoft.AspNetCore.Components.Routing.NavLink> otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `MyComponent` prefiksem (na przykład `https://localhost:5001/MyComponent` i `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-211">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="c1c3f-212">Dodatkowe <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-212">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="c1c3f-213">W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnik zawiera `target` atrybut:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-213">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="c1c3f-214">Renderuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-214">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="c1c3f-215">Ze względu na sposób, który :::no-loc(Blazor)::: renderuje zawartość podrzędną, `NavLink` składniki renderowania wewnątrz `for` pętli wymagają zmiennej lokalnego indeksu, jeśli zmienna pętli zwiększania jest używana w `NavLink` zawartości składnika (potomne):</span><span class="sxs-lookup"><span data-stu-id="c1c3f-215">Due to the way that :::no-loc(Blazor)::: renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="c1c3f-216">Użycie zmiennej index w tym scenariuszu jest wymaganiem dla **dowolnego** składnika podrzędnego, który używa zmiennej pętli w jej [zawartości podrzędnej](xref:blazor/components/index#child-content), a nie tylko `NavLink` składnika.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-216">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="c1c3f-217">Alternatywnie możesz użyć `foreach` pętli z <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="c1c3f-217">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="c1c3f-218">Pomoc dotycząca stanu identyfikatora URI i nawigacji</span><span class="sxs-lookup"><span data-stu-id="c1c3f-218">URI and navigation state helpers</span></span>

<span data-ttu-id="c1c3f-219">Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacją w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-219">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="c1c3f-220"><xref:Microsoft.AspNetCore.Components.NavigationManager> zawiera zdarzenie i metody przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-220"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="c1c3f-221">Członek</span><span class="sxs-lookup"><span data-stu-id="c1c3f-221">Member</span></span> | <span data-ttu-id="c1c3f-222">Opis</span><span class="sxs-lookup"><span data-stu-id="c1c3f-222">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="c1c3f-223">Pobiera bieżący bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-223">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="c1c3f-224">Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-224">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="c1c3f-225">Zazwyczaj <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpowiada `href` atrybutowi `<base>` elementu dokumentu w `wwwroot/index.html` ( :::no-loc(Blazor WebAssembly)::: ) lub `Pages/_Host.cshtml` ( :::no-loc(Blazor Server)::: ).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-225">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="c1c3f-226">Przechodzi do określonego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-226">Navigates to the specified URI.</span></span> <span data-ttu-id="c1c3f-227">Jeśli `forceLoad` jest `true` :</span><span class="sxs-lookup"><span data-stu-id="c1c3f-227">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="c1c3f-228">Routing po stronie klienta jest pomijany.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-228">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="c1c3f-229">W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-229">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="c1c3f-230">Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-230">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="c1c3f-231">Konwertuje względny identyfikator URI na bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-231">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="c1c3f-232">Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej przez <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-232">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="c1c3f-233">Poniższy składnik przechodzi do `Counter` składnika aplikacji po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-233">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="c1c3f-234">Poniższy składnik obsługuje zdarzenie zmiany lokalizacji przez subskrybowanie do <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-234">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c1c3f-235">Metoda jest odłączana, `HandleLocationChanged` gdy `Dispose` jest wywoływana przez platformę.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-235">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="c1c3f-236">Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-236">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="c1c3f-237"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> zawiera następujące informacje o zdarzeniu:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-237"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="c1c3f-238"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adres URL nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-238"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="c1c3f-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Jeśli `true` :::no-loc(Blazor)::: przechwytuje nawigację z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, :::no-loc(Blazor)::: intercepted the navigation from the browser.</span></span> <span data-ttu-id="c1c3f-240">Jeśli `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wystąpiła nawigacja.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-240">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="c1c3f-241">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-241">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="c1c3f-242">Ciąg zapytania i parametry analizy</span><span class="sxs-lookup"><span data-stu-id="c1c3f-242">Query string and parse parameters</span></span>

<span data-ttu-id="c1c3f-243">Ciąg zapytania żądania można uzyskać z <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-243">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="c1c3f-244">Aby przeanalizować parametry ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="c1c3f-244">To parse a query string's parameters:</span></span>

* <span data-ttu-id="c1c3f-245">Dodaj odwołanie do pakietu dla [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="c1c3f-245">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="c1c3f-246">Uzyskaj wartość po przeanalizowaniu ciągu zapytania za pomocą elementu <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-246">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

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

<span data-ttu-id="c1c3f-247">Symbol zastępczy `{KEY}` w powyższym przykładzie jest kluczem parametrów ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="c1c3f-247">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="c1c3f-248">Na przykład para klucz adresu URL `?ship=Tardis` używa klucza `ship` .</span><span class="sxs-lookup"><span data-stu-id="c1c3f-248">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
