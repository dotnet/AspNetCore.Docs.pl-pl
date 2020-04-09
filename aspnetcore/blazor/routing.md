---
title: Routing ASP.NET Blazor Core
author: guardrex
description: Dowiedz się, jak rozsyłać żądania w aplikacjach i o składniku NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218898"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="bb956-103">ASP.NET rdzeń Blazor routingu</span><span class="sxs-lookup"><span data-stu-id="bb956-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="bb956-104">Przez [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bb956-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="bb956-105">Dowiedz się, jak rozsyłać żądania i jak używać tego składnika `NavLink` do tworzenia łączy nawigacyjnych w aplikacjach Blazor.</span><span class="sxs-lookup"><span data-stu-id="bb956-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="bb956-106">ASP.NET integracja routingu punktów końcowych core</span><span class="sxs-lookup"><span data-stu-id="bb956-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="bb956-107">Blazor Server jest zintegrowany [z ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="bb956-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="bb956-108">Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych z `MapBlazorHub` w: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="bb956-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="bb956-109">Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do strony Razor, która działa jako host dla części po stronie serwera aplikacji Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="bb956-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="bb956-110">Zgodnie z konwencją strona *hosta* nosi zwykle nazwę *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb956-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="bb956-111">Trasa określona w pliku hosta jest nazywana *trasą rezerwową,* ponieważ działa z niskim priorytetem w dopasowywaniu trasy.</span><span class="sxs-lookup"><span data-stu-id="bb956-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="bb956-112">Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="bb956-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="bb956-113">Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania aplikacji Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="bb956-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="bb956-114">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="bb956-114">Route templates</span></span>

<span data-ttu-id="bb956-115">Komponent `Router` umożliwia routing do każdego komponentu o określonej trasie.</span><span class="sxs-lookup"><span data-stu-id="bb956-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="bb956-116">Składnik `Router` pojawi się w pliku *App.razor:*</span><span class="sxs-lookup"><span data-stu-id="bb956-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="bb956-117">Po skompilowaniu pliku `@page` *.brzytwa* z dyrektywą <xref:Microsoft.AspNetCore.Components.RouteAttribute> wygenerowana klasa jest podana określająca szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="bb956-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="bb956-118">W czasie wykonywania `RouteView` składnik:</span><span class="sxs-lookup"><span data-stu-id="bb956-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="bb956-119">Odbiera `RouteData` od `Router` wraz z dowolnymi żądanymi parametrami.</span><span class="sxs-lookup"><span data-stu-id="bb956-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="bb956-120">Renderuje określony komponent z jego układem (lub opcjonalnym układem domyślnym) przy użyciu określonych parametrów.</span><span class="sxs-lookup"><span data-stu-id="bb956-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="bb956-121">Opcjonalnie można określić `DefaultLayout` parametr z klasą układu, która będzie używana dla składników, które nie określają układu.</span><span class="sxs-lookup"><span data-stu-id="bb956-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="bb956-122">Domyślne szablony Blazor `MainLayout` określają składnik.</span><span class="sxs-lookup"><span data-stu-id="bb956-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="bb956-123">*MainLayout.brzytwa* znajduje się w folderze *udostępnionym* projektu szablonu.</span><span class="sxs-lookup"><span data-stu-id="bb956-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="bb956-124">Aby uzyskać więcej informacji <xref:blazor/layouts>na temat układów, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bb956-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="bb956-125">Do komponentu można zastosować wiele szablonów marszrut.</span><span class="sxs-lookup"><span data-stu-id="bb956-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="bb956-126">Następujący składnik odpowiada na `/BlazorRoute` żądania `/DifferentBlazorRoute`i:</span><span class="sxs-lookup"><span data-stu-id="bb956-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="bb956-127">Aby adresy URL zostały poprawnie rozwiązane, `<base>` aplikacja musi zawierać znacznik w pliku *wwwroot/index.html* (Blazor WebAssembly) lub *Pages/_Host.cshtml* (Blazor Server) ze ścieżką podstawową aplikacji określoną w `href` atrybucie (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="bb956-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="bb956-128">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="bb956-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="bb956-129">Dostarczanie zawartości niestandardowej, gdy nie zostanie znaleziona zawartość</span><span class="sxs-lookup"><span data-stu-id="bb956-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="bb956-130">Składnik `Router` umożliwia aplikacji określenie zawartości niestandardowej, jeśli zawartość nie zostanie znaleziona dla żądanej trasy.</span><span class="sxs-lookup"><span data-stu-id="bb956-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="bb956-131">W pliku *App.razor* ustaw niestandardową `NotFound` zawartość w `Router` parametrze szablonu składnika:</span><span class="sxs-lookup"><span data-stu-id="bb956-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="bb956-132">Zawartość tagów `<NotFound>` może zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="bb956-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="bb956-133">Aby zastosować domyślny `NotFound` układ <xref:blazor/layouts>do zawartości, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bb956-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="bb956-134">Droga do komponentów z wielu złożeń</span><span class="sxs-lookup"><span data-stu-id="bb956-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="bb956-135">Parametr `AdditionalAssemblies` służy do określania dodatkowych `Router` złożeń dla komponentu, które mają być uwzględniane podczas wyszukiwania komponentów rutowalnych.</span><span class="sxs-lookup"><span data-stu-id="bb956-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="bb956-136">Określone zestawy są uważane za `AppAssembly`oprócz -specified zestawu.</span><span class="sxs-lookup"><span data-stu-id="bb956-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="bb956-137">W poniższym `Component1` przykładzie jest rutowalnym składnikiem zdefiniowanym w bibliotece klas, do których istnieje odwołanie.</span><span class="sxs-lookup"><span data-stu-id="bb956-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="bb956-138">Poniższy `AdditionalAssemblies` przykład powoduje obsługę `Component1`routingu dla:</span><span class="sxs-lookup"><span data-stu-id="bb956-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="bb956-139">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="bb956-139">Route parameters</span></span>

<span data-ttu-id="bb956-140">Router używa parametrów trasy do wypełniania odpowiednich parametrów komponentu o tej samej nazwie (bez uwzględniania wielkości liter):</span><span class="sxs-lookup"><span data-stu-id="bb956-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="bb956-141">Parametry opcjonalne nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="bb956-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="bb956-142">W `@page` poprzednim przykładzie stosowane są dwie dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="bb956-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="bb956-143">Pierwszy umożliwia nawigację do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="bb956-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="bb956-144">Druga `@page` dyrektywa przyjmuje `{text}` parametr trasy i przypisuje `Text` wartość do właściwości.</span><span class="sxs-lookup"><span data-stu-id="bb956-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="bb956-145">Ograniczenia trasy</span><span class="sxs-lookup"><span data-stu-id="bb956-145">Route constraints</span></span>

<span data-ttu-id="bb956-146">Ograniczenie trasy wymusza dopasowywanie typu w segmencie trasy do komponentu.</span><span class="sxs-lookup"><span data-stu-id="bb956-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="bb956-147">W poniższym przykładzie trasa `Users` do komponentu jest zgodna tylko wtedy, gdy:</span><span class="sxs-lookup"><span data-stu-id="bb956-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="bb956-148">Segment `Id` trasy znajduje się w adresie URL żądania.</span><span class="sxs-lookup"><span data-stu-id="bb956-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="bb956-149">Segment `Id` jest całkowitej liczby`int`( ).</span><span class="sxs-lookup"><span data-stu-id="bb956-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="bb956-150">Ograniczenia trasy pokazane w poniższej tabeli są dostępne.</span><span class="sxs-lookup"><span data-stu-id="bb956-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="bb956-151">Aby uzyskać ograniczenia trasy, które pasują do kultury niezmiennej, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="bb956-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="bb956-152">Ograniczenie</span><span class="sxs-lookup"><span data-stu-id="bb956-152">Constraint</span></span> | <span data-ttu-id="bb956-153">Przykład</span><span class="sxs-lookup"><span data-stu-id="bb956-153">Example</span></span>           | <span data-ttu-id="bb956-154">Przykładowe dopasowania</span><span class="sxs-lookup"><span data-stu-id="bb956-154">Example Matches</span></span>                                                                  | <span data-ttu-id="bb956-155">Niezmienna</span><span class="sxs-lookup"><span data-stu-id="bb956-155">Invariant</span></span><br><span data-ttu-id="bb956-156">kultura</span><span class="sxs-lookup"><span data-stu-id="bb956-156">culture</span></span><br><span data-ttu-id="bb956-157">parowanie</span><span class="sxs-lookup"><span data-stu-id="bb956-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="bb956-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="bb956-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="bb956-159">Nie</span><span class="sxs-lookup"><span data-stu-id="bb956-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="bb956-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="bb956-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="bb956-161">Tak</span><span class="sxs-lookup"><span data-stu-id="bb956-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="bb956-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="bb956-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="bb956-163">Tak</span><span class="sxs-lookup"><span data-stu-id="bb956-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="bb956-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="bb956-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="bb956-165">Tak</span><span class="sxs-lookup"><span data-stu-id="bb956-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="bb956-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="bb956-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="bb956-167">Tak</span><span class="sxs-lookup"><span data-stu-id="bb956-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="bb956-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="bb956-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="bb956-169">Nie</span><span class="sxs-lookup"><span data-stu-id="bb956-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="bb956-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="bb956-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="bb956-171">Tak</span><span class="sxs-lookup"><span data-stu-id="bb956-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="bb956-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="bb956-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="bb956-173">Tak</span><span class="sxs-lookup"><span data-stu-id="bb956-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="bb956-174">Ograniczenia trasy, które weryfikują adres URL i są `int` `DateTime`konwertowane na typ CLR (na przykład lub ) zawsze używają kultury niezmiennej.</span><span class="sxs-lookup"><span data-stu-id="bb956-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="bb956-175">Te ograniczenia zakładają, że adres URL nie jest zlokalizowany.</span><span class="sxs-lookup"><span data-stu-id="bb956-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="bb956-176">Routing z adresami URL zawierającymi kropki</span><span class="sxs-lookup"><span data-stu-id="bb956-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="bb956-177">W aplikacjach Blazor Server domyślną trasą w *_Host.cshtml* jest `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="bb956-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="bb956-178">Adres URL żądania zawierający`.`kropkę ( ) nie jest dopasowyany do domyślnej trasy, ponieważ adres URL wydaje się żądać pliku.</span><span class="sxs-lookup"><span data-stu-id="bb956-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="bb956-179">Aplikacja Blazor zwraca *odpowiedź 404 — nie znaleziono* dla pliku statycznego, który nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="bb956-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="bb956-180">Aby użyć tras zawierających kropkę, należy skonfigurować *_Host.cshtml* z następującym szablonem trasy:</span><span class="sxs-lookup"><span data-stu-id="bb956-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="bb956-181">Szablon `"/{**path}"` zawiera:</span><span class="sxs-lookup"><span data-stu-id="bb956-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="bb956-182">Podwójna gwiazdka *catch-all* składni`**`( ) do przechwytywania ścieżki przez wiele granic`/`folderów bez kodowania ukośniki do przodu ( ).</span><span class="sxs-lookup"><span data-stu-id="bb956-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="bb956-183">`path`nazwę parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="bb956-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="bb956-184">*Składnia parametrów catch-all* (`*`/`**`) **nie** jest obsługiwana w składnikach Razor (*.brzytwa*).</span><span class="sxs-lookup"><span data-stu-id="bb956-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="bb956-185">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="bb956-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="bb956-186">Komponent NavLink</span><span class="sxs-lookup"><span data-stu-id="bb956-186">NavLink component</span></span>

<span data-ttu-id="bb956-187">Podczas `NavLink` tworzenia łączy nawigacyjnych należy`<a>`używać składnika zamiast elementów hiperłącza HTML ( ) .</span><span class="sxs-lookup"><span data-stu-id="bb956-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="bb956-188">Składnik `NavLink` zachowuje się `<a>` jak element, z tą `active` różnicą, że `href` przełącza klasę CSS na podstawie tego, czy jest zgodna z bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="bb956-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="bb956-189">Klasa `active` pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną wśród wyświetlanych łączy nawigacyjnych.</span><span class="sxs-lookup"><span data-stu-id="bb956-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="bb956-190">Następujący `NavMenu` składnik tworzy pasek nawigacyjny [Bootstrap,](https://getbootstrap.com/docs/) który `NavLink` pokazuje, jak używać składników:</span><span class="sxs-lookup"><span data-stu-id="bb956-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="bb956-191">Istnieją dwie `NavLinkMatch` opcje, które można `Match` przypisać do `<NavLink>` atrybutu elementu:</span><span class="sxs-lookup"><span data-stu-id="bb956-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="bb956-192">`NavLinkMatch.All`&ndash; Jest `NavLink` aktywny, gdy pasuje do całego bieżącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="bb956-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="bb956-193">`NavLinkMatch.Prefix`(*domyślnie*) &ndash; Jest `NavLink` aktywny, gdy pasuje do dowolnego prefiksu bieżącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="bb956-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="bb956-194">W poprzednim przykładzie strona `NavLink` `href=""` główna pasuje do domowego `active` adresu URL i odbiera tylko klasę CSS `https://localhost:5001/`przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="bb956-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="bb956-195">`NavLink` Drugi otrzymuje `active` klasę, gdy użytkownik odwiedza `MyComponent` dowolny adres URL `https://localhost:5001/MyComponent` z `https://localhost:5001/MyComponent/AnotherSegment`prefiksem (na przykład i ).</span><span class="sxs-lookup"><span data-stu-id="bb956-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="bb956-196">Dodatkowe `NavLink` atrybuty składnika są przekazywane do renderowanego tagu zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="bb956-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="bb956-197">W poniższym przykładzie `NavLink` składnik `target` zawiera atrybut:</span><span class="sxs-lookup"><span data-stu-id="bb956-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="bb956-198">Renderowane są następujące znaczniki HTML:</span><span class="sxs-lookup"><span data-stu-id="bb956-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="bb956-199">Pomocnicy URI i stanu nawigacji</span><span class="sxs-lookup"><span data-stu-id="bb956-199">URI and navigation state helpers</span></span>

<span data-ttu-id="bb956-200">Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacji w kodzie języka C#.</span><span class="sxs-lookup"><span data-stu-id="bb956-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="bb956-201">`NavigationManager`zawiera zdarzenie i metody pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="bb956-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="bb956-202">Członek</span><span class="sxs-lookup"><span data-stu-id="bb956-202">Member</span></span> | <span data-ttu-id="bb956-203">Opis</span><span class="sxs-lookup"><span data-stu-id="bb956-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="bb956-204">Identyfikator uri</span><span class="sxs-lookup"><span data-stu-id="bb956-204">Uri</span></span> | <span data-ttu-id="bb956-205">Pobiera bieżący bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="bb956-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="bb956-206">Baseuri</span><span class="sxs-lookup"><span data-stu-id="bb956-206">BaseUri</span></span> | <span data-ttu-id="bb956-207">Pobiera podstawowy identyfikator URI (z ukośnikiem kończącym), który może być dołączany do względnych ścieżek URI w celu wytworzenia bezwzględnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="bb956-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="bb956-208">`BaseUri` Zazwyczaj odpowiada atrybutowi `href` `<base>` elementu dokumentu w *wwwroot/index.html* (WebAssembly)Blazor lub *Pages/_Host.cshtml* (Blazor Serwer).</span><span class="sxs-lookup"><span data-stu-id="bb956-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="bb956-209">Navigateto</span><span class="sxs-lookup"><span data-stu-id="bb956-209">NavigateTo</span></span> | <span data-ttu-id="bb956-210">Przechodzi do określonego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="bb956-210">Navigates to the specified URI.</span></span> <span data-ttu-id="bb956-211">Jeśli `forceLoad` `true`jest:</span><span class="sxs-lookup"><span data-stu-id="bb956-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="bb956-212">Routing po stronie klienta jest pomijany.</span><span class="sxs-lookup"><span data-stu-id="bb956-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="bb956-213">Przeglądarka jest zmuszona do załadowania nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="bb956-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="bb956-214">Locationchanged</span><span class="sxs-lookup"><span data-stu-id="bb956-214">LocationChanged</span></span> | <span data-ttu-id="bb956-215">Zdarzenie, które uruchamia się po zmianie lokalizacji nawigacji.</span><span class="sxs-lookup"><span data-stu-id="bb956-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="bb956-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="bb956-216">ToAbsoluteUri</span></span> | <span data-ttu-id="bb956-217">Konwertuje względny identyfikator URI na bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="bb956-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="bb956-218"><span style="word-break:normal;word-wrap:normal">Ścieżka ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="bb956-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="bb956-219">Biorąc pod uwagę podstawowy identyfikator URI (na `GetBaseUri`przykład identyfikator URI wcześniej zwrócony przez), konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI.</span><span class="sxs-lookup"><span data-stu-id="bb956-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="bb956-220">Następujący składnik przechodzi do składnika `Counter` aplikacji po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="bb956-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="bb956-221">Poniższy składnik obsługuje zdarzenie zmiany lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="bb956-221">The following component handles a location changed event.</span></span> <span data-ttu-id="bb956-222">Metoda `HandleLocationChanged` jest odłączana, `Dispose` gdy jest wywoływana przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="bb956-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="bb956-223">Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.</span><span class="sxs-lookup"><span data-stu-id="bb956-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implement IDisposable
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

<span data-ttu-id="bb956-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>zawiera następujące informacje o zdarzeniu:</span><span class="sxs-lookup"><span data-stu-id="bb956-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="bb956-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; Adres URL nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="bb956-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="bb956-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Jeśli `true` Blazor , przechwycono nawigację z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="bb956-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="bb956-227">Jeśli `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) spowodowało nawigację.</span><span class="sxs-lookup"><span data-stu-id="bb956-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="bb956-228">Aby uzyskać więcej informacji <xref:blazor/lifecycle#component-disposal-with-idisposable>na temat usuwania komponentów, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bb956-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
