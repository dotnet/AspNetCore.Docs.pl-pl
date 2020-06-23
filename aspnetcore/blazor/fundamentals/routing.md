---
title: BlazorRouting ASP.NET Core
author: guardrex
description: Dowiedz się, jak kierować żądania w aplikacjach i informacje o składniku NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: fde30109395065014433bebde52a9eb22458c451
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242748"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="47435-103">BlazorRouting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47435-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="47435-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="47435-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="47435-105">Dowiedz się, jak kierować żądania i jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnika do tworzenia linków nawigacji w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="47435-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="47435-106">ASP.NET Core integracja z routingiem punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="47435-106">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="47435-107">Serwer jest zintegrowany z [ASP.NET Core routingiem punktów końcowych](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="47435-107"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="47435-108">Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za pomocą <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> programu w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="47435-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="47435-109">Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do Razor strony, które pełnią rolę hosta dla części aplikacji serwerowej po stronie serwera Blazor .</span><span class="sxs-lookup"><span data-stu-id="47435-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="47435-110">Zgodnie z Konwencją strona *hosta* ma zwykle nazwę `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="47435-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="47435-111">Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras.</span><span class="sxs-lookup"><span data-stu-id="47435-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="47435-112">Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="47435-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="47435-113">Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania działania Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="47435-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="47435-114">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="47435-114">Route templates</span></span>

<span data-ttu-id="47435-115"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia kierowanie do każdego składnika z określoną trasą.</span><span class="sxs-lookup"><span data-stu-id="47435-115">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="47435-116"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik pojawi się w `App.razor` pliku:</span><span class="sxs-lookup"><span data-stu-id="47435-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

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

<span data-ttu-id="47435-117">Gdy `.razor` plik z `@page` dyrektywą jest kompilowany, wygenerowana Klasa jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="47435-117">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="47435-118">W środowisku uruchomieniowym <xref:Microsoft.AspNetCore.Components.RouteView> składnik:</span><span class="sxs-lookup"><span data-stu-id="47435-118">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="47435-119">Odbiera <xref:Microsoft.AspNetCore.Components.RouteData> od siebie <xref:Microsoft.AspNetCore.Components.Routing.Router> wraz z dowolnymi żądanymi parametrami.</span><span class="sxs-lookup"><span data-stu-id="47435-119">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="47435-120">Renderuje określony składnik za pomocą układu (lub opcjonalnego układu domyślnego) przy użyciu określonych parametrów.</span><span class="sxs-lookup"><span data-stu-id="47435-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="47435-121">Opcjonalnie można określić <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr z klasą układu, która ma być używana dla składników, które nie określają układu.</span><span class="sxs-lookup"><span data-stu-id="47435-121">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="47435-122">Domyślne Blazor Szablony określają `MainLayout` składnik.</span><span class="sxs-lookup"><span data-stu-id="47435-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="47435-123">`MainLayout.razor`znajduje się w folderze szablonu projektu `Shared` .</span><span class="sxs-lookup"><span data-stu-id="47435-123">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="47435-124">Aby uzyskać więcej informacji na temat układów, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="47435-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="47435-125">Do składnika można zastosować wiele szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="47435-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="47435-126">Poniższy składnik odpowiada na żądania dla `/BlazorRoute` i `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="47435-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="47435-127">Aby adresy URL zostały poprawnie rozpoznane, aplikacja musi zawierać `<base>` tag w `wwwroot/index.html` pliku ( Blazor webassembly) lub `Pages/_Host.cshtml` pliku ( Blazor serwer) z ścieżką bazową aplikacji określoną w `href` atrybucie ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="47435-127">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="47435-128">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="47435-128">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="47435-129">Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości</span><span class="sxs-lookup"><span data-stu-id="47435-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="47435-130"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.</span><span class="sxs-lookup"><span data-stu-id="47435-130">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="47435-131">W `App.razor` pliku Ustaw zawartość niestandardową w <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametrze szablonu <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika:</span><span class="sxs-lookup"><span data-stu-id="47435-131">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="47435-132">Zawartość `<NotFound>` tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="47435-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="47435-133">Aby zastosować domyślny układ do <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> zawartości, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="47435-133">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="47435-134">Kierowanie do składników z wielu zestawów</span><span class="sxs-lookup"><span data-stu-id="47435-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="47435-135">Użyj <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametru, aby określić dodatkowe zestawy dla <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika do uwzględnienia podczas wyszukiwania składników routingu.</span><span class="sxs-lookup"><span data-stu-id="47435-135">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="47435-136">Określone zestawy są traktowane jako uzupełnienie `AppAssembly` określonego zestawu.</span><span class="sxs-lookup"><span data-stu-id="47435-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="47435-137">W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w bibliotece klas, do której się odwołuje.</span><span class="sxs-lookup"><span data-stu-id="47435-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="47435-138">W poniższym <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> przykładzie przedstawiono obsługę routingu dla `Component1` :</span><span class="sxs-lookup"><span data-stu-id="47435-138">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="47435-139">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="47435-139">Route parameters</span></span>

<span data-ttu-id="47435-140">Router używa parametrów trasy do wypełniania odpowiednich parametrów składnika o tej samej nazwie (bez uwzględniania wielkości liter):</span><span class="sxs-lookup"><span data-stu-id="47435-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="47435-141">Parametry opcjonalne nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="47435-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="47435-142">`@page`W poprzednim przykładzie są stosowane dwie dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="47435-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="47435-143">Pierwszy zezwala na nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="47435-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="47435-144">Druga `@page` dyrektywa przyjmuje `{text}` parametr Route i przypisuje wartość do `Text` właściwości.</span><span class="sxs-lookup"><span data-stu-id="47435-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="47435-145">Ograniczenia trasy</span><span class="sxs-lookup"><span data-stu-id="47435-145">Route constraints</span></span>

<span data-ttu-id="47435-146">Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.</span><span class="sxs-lookup"><span data-stu-id="47435-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="47435-147">W poniższym przykładzie trasa do `Users` składnika dopasowuje się tylko wtedy, gdy:</span><span class="sxs-lookup"><span data-stu-id="47435-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="47435-148">`Id`Segment trasy jest obecny w adresie URL żądania.</span><span class="sxs-lookup"><span data-stu-id="47435-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="47435-149">`Id`Segment jest liczbą całkowitą ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="47435-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="47435-150">Dostępne są ograniczenia trasy podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="47435-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="47435-151">W przypadku ograniczeń trasy, które pasują do niezmiennej kultury, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="47435-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="47435-152">Typu</span><span class="sxs-lookup"><span data-stu-id="47435-152">Constraint</span></span> | <span data-ttu-id="47435-153">Przykład</span><span class="sxs-lookup"><span data-stu-id="47435-153">Example</span></span>           | <span data-ttu-id="47435-154">Przykładowe dopasowania</span><span class="sxs-lookup"><span data-stu-id="47435-154">Example Matches</span></span>                                                                  | <span data-ttu-id="47435-155">Niezmiennej</span><span class="sxs-lookup"><span data-stu-id="47435-155">Invariant</span></span><br><span data-ttu-id="47435-156">kultura</span><span class="sxs-lookup"><span data-stu-id="47435-156">culture</span></span><br><span data-ttu-id="47435-157">parowanie</span><span class="sxs-lookup"><span data-stu-id="47435-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="47435-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="47435-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="47435-159">Nie</span><span class="sxs-lookup"><span data-stu-id="47435-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="47435-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="47435-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="47435-161">Tak</span><span class="sxs-lookup"><span data-stu-id="47435-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="47435-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="47435-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="47435-163">Tak</span><span class="sxs-lookup"><span data-stu-id="47435-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="47435-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="47435-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="47435-165">Tak</span><span class="sxs-lookup"><span data-stu-id="47435-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="47435-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="47435-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="47435-167">Tak</span><span class="sxs-lookup"><span data-stu-id="47435-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="47435-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="47435-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="47435-169">Nie</span><span class="sxs-lookup"><span data-stu-id="47435-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="47435-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="47435-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="47435-171">Tak</span><span class="sxs-lookup"><span data-stu-id="47435-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="47435-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="47435-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="47435-173">Tak</span><span class="sxs-lookup"><span data-stu-id="47435-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="47435-174">Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub <xref:System.DateTime> ), zawsze używają niezmiennej kultury.</span><span class="sxs-lookup"><span data-stu-id="47435-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="47435-175">W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.</span><span class="sxs-lookup"><span data-stu-id="47435-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="47435-176">Routing z adresami URL zawierającymi kropki</span><span class="sxs-lookup"><span data-stu-id="47435-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="47435-177">W obszarze Blazor aplikacje serwera domyślną trasą w programie `_Host.cshtml` jest `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="47435-177">In Blazor Server apps, the default route in `_Host.cshtml` is `/` (`@page "/"`).</span></span> <span data-ttu-id="47435-178">Adres URL żądania, który zawiera kropkę ( `.` ) nie pasuje do trasy domyślnej, ponieważ adres URL wygląda na żądanie pliku.</span><span class="sxs-lookup"><span data-stu-id="47435-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="47435-179">BlazorAplikacja zwraca *404 — nie odnaleziono* odpowiedzi dla pliku statycznego, który nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="47435-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="47435-180">Aby użyć tras zawierających kropkę, skonfiguruj `_Host.cshtml` przy użyciu następującego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="47435-180">To use routes that contain a dot, configure `_Host.cshtml` with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="47435-181">`"/{**path}"`Szablon zawiera:</span><span class="sxs-lookup"><span data-stu-id="47435-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="47435-182">Podwójna gwiazdka *catch-all* ( `**` ) do przechwytywania ścieżki między wieloma granicami folderów bez kodowania ukośników ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="47435-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="47435-183">`path`Nazwa parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="47435-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="47435-184">Składnia *catch-all* parametru ( `*` / `**` ) **nie** jest obsługiwana w Razor składnikach ( `.razor` ).</span><span class="sxs-lookup"><span data-stu-id="47435-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (`.razor`).</span></span>

<span data-ttu-id="47435-185">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="47435-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="47435-186">Składnik NavLink</span><span class="sxs-lookup"><span data-stu-id="47435-186">NavLink component</span></span>

<span data-ttu-id="47435-187"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Podczas tworzenia linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML ( `<a>` ).</span><span class="sxs-lookup"><span data-stu-id="47435-187">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="47435-188"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia `active` klasy CSS w zależności od tego, czy jest on `href` zgodny z bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="47435-188">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="47435-189">`active`Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="47435-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="47435-190">Poniższy `NavMenu` składnik tworzy [`Bootstrap`](https://getbootstrap.com/docs/) pasek nawigacyjny, który pokazuje, jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składników:</span><span class="sxs-lookup"><span data-stu-id="47435-190">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="47435-191">Dostępne są dwie <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="47435-191">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="47435-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Jest aktywny, gdy jest zgodny z całym bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="47435-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="47435-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*ustawienie domyślne*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> jest aktywny, gdy pasuje do dowolnego PREFIKSU bieżącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="47435-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="47435-194">W poprzednim przykładzie Strona główna <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` odpowiada GŁÓWNEmu adresowi URL i odbiera tylko `active` klasy CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="47435-194">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="47435-195">Druga <xref:Microsoft.AspNetCore.Components.Routing.NavLink> otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `MyComponent` prefiksem (na przykład `https://localhost:5001/MyComponent` i `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="47435-195">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="47435-196">Dodatkowe <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="47435-196">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="47435-197">W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnik zawiera `target` atrybut:</span><span class="sxs-lookup"><span data-stu-id="47435-197">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="47435-198">Renderuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="47435-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="47435-199">Pomoc dotycząca stanu identyfikatora URI i nawigacji</span><span class="sxs-lookup"><span data-stu-id="47435-199">URI and navigation state helpers</span></span>

<span data-ttu-id="47435-200">Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacją w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="47435-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="47435-201"><xref:Microsoft.AspNetCore.Components.NavigationManager>zawiera zdarzenie i metody przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="47435-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="47435-202">Członek</span><span class="sxs-lookup"><span data-stu-id="47435-202">Member</span></span> | <span data-ttu-id="47435-203">Opis</span><span class="sxs-lookup"><span data-stu-id="47435-203">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="47435-204">Pobiera bieżący bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="47435-204">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="47435-205">Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="47435-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="47435-206">Zazwyczaj <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpowiada `href` atrybutowi `<base>` elementu dokumentu w `wwwroot/index.html` ( Blazor webassembly) lub `Pages/_Host.cshtml` ( Blazor serwer).</span><span class="sxs-lookup"><span data-stu-id="47435-206">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="47435-207">Przechodzi do określonego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="47435-207">Navigates to the specified URI.</span></span> <span data-ttu-id="47435-208">Jeśli `forceLoad` jest `true` :</span><span class="sxs-lookup"><span data-stu-id="47435-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="47435-209">Routing po stronie klienta jest pomijany.</span><span class="sxs-lookup"><span data-stu-id="47435-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="47435-210">W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="47435-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="47435-211">Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji.</span><span class="sxs-lookup"><span data-stu-id="47435-211">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="47435-212">Konwertuje względny identyfikator URI na bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="47435-212">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="47435-213">Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej przez <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI.</span><span class="sxs-lookup"><span data-stu-id="47435-213">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="47435-214">Poniższy składnik przechodzi do `Counter` składnika aplikacji po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="47435-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="47435-215">Poniższy składnik obsługuje zdarzenie zmiany lokalizacji przez ustawienie <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="47435-215">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="47435-216">Metoda jest odłączana, `HandleLocationChanged` gdy `Dispose` jest wywoływana przez platformę.</span><span class="sxs-lookup"><span data-stu-id="47435-216">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="47435-217">Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.</span><span class="sxs-lookup"><span data-stu-id="47435-217">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="47435-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>zawiera następujące informacje o zdarzeniu:</span><span class="sxs-lookup"><span data-stu-id="47435-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="47435-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adres URL nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="47435-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="47435-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Jeśli `true` Blazor przechwytuje nawigację z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="47435-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="47435-221">Jeśli `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wystąpiła nawigacja.</span><span class="sxs-lookup"><span data-stu-id="47435-221">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="47435-222">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="47435-222">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
