---
title: Routing Blazor ASP.NET Core
author: guardrex
description: Dowiedz się, jak kierować żądania w aplikacjach i informacje o składniku NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 017fd4d3ab45b75355dabb400ff0e5cbf7009d82
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771210"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="f69e2-103">ASP.NET Core Routing Blazor</span><span class="sxs-lookup"><span data-stu-id="f69e2-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="f69e2-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f69e2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f69e2-105">Dowiedz się, jak kierować żądania oraz jak używać `NavLink` składnika do tworzenia linków nawigacji w aplikacjach Blazor.</span><span class="sxs-lookup"><span data-stu-id="f69e2-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="f69e2-106">ASP.NET Core integracja z routingiem punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="f69e2-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="f69e2-107">Serwer Blazor jest zintegrowany z [routingiem punktu końcowego ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="f69e2-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="f69e2-108">Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za `MapBlazorHub` pomocą `Startup.Configure`programu w programie:</span><span class="sxs-lookup"><span data-stu-id="f69e2-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="f69e2-109">Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do strony Razor, która działa jako host dla części serwerowej aplikacji Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f69e2-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="f69e2-110">Zgodnie z Konwencją strona *hosta* ma zwykle nazwę *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f69e2-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="f69e2-111">Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras.</span><span class="sxs-lookup"><span data-stu-id="f69e2-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="f69e2-112">Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="f69e2-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="f69e2-113">Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania działania aplikacji serwera Blazor.</span><span class="sxs-lookup"><span data-stu-id="f69e2-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="f69e2-114">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="f69e2-114">Route templates</span></span>

<span data-ttu-id="f69e2-115">`Router` Składnik umożliwia kierowanie do każdego składnika z określoną trasą.</span><span class="sxs-lookup"><span data-stu-id="f69e2-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="f69e2-116">`Router` Składnik pojawi się w pliku *App. Razor* :</span><span class="sxs-lookup"><span data-stu-id="f69e2-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="f69e2-117">Gdy plik *Razor* z `@page` dyrektywą jest kompilowany, wygenerowana Klasa jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="f69e2-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="f69e2-118">W środowisku uruchomieniowym `RouteView` składnik:</span><span class="sxs-lookup"><span data-stu-id="f69e2-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="f69e2-119">Odbiera `RouteData` od siebie `Router` wraz z dowolnymi żądanymi parametrami.</span><span class="sxs-lookup"><span data-stu-id="f69e2-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="f69e2-120">Renderuje określony składnik za pomocą układu (lub opcjonalnego układu domyślnego) przy użyciu określonych parametrów.</span><span class="sxs-lookup"><span data-stu-id="f69e2-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="f69e2-121">Opcjonalnie można określić `DefaultLayout` parametr z klasą układu, która ma być używana dla składników, które nie określają układu.</span><span class="sxs-lookup"><span data-stu-id="f69e2-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="f69e2-122">Domyślne szablony Blazor określają `MainLayout` składnik.</span><span class="sxs-lookup"><span data-stu-id="f69e2-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="f69e2-123">*MainLayout. Razor* znajduje się w folderze *udostępnionym* projektu szablonu.</span><span class="sxs-lookup"><span data-stu-id="f69e2-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="f69e2-124">Aby uzyskać więcej informacji na temat układów <xref:blazor/layouts>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="f69e2-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="f69e2-125">Do składnika można zastosować wiele szablonów tras.</span><span class="sxs-lookup"><span data-stu-id="f69e2-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="f69e2-126">Poniższy składnik odpowiada na żądania dla `/BlazorRoute` i: `/DifferentBlazorRoute`</span><span class="sxs-lookup"><span data-stu-id="f69e2-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="f69e2-127">Aby adresy URL zostały poprawnie rozpoznane, aplikacja musi `<base>` zawierać tag w pliku *wwwroot/index.html* (Blazor Webassembly) lub *Pages/_Host. cshtml* (Blazor Server) z ścieżką bazową aplikacji określoną w `href` atrybucie (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="f69e2-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="f69e2-128">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="f69e2-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="f69e2-129">Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości</span><span class="sxs-lookup"><span data-stu-id="f69e2-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="f69e2-130">`Router` Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.</span><span class="sxs-lookup"><span data-stu-id="f69e2-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="f69e2-131">W pliku *App. Razor* Ustaw zawartość niestandardową w parametrze `NotFound` szablonu `Router` składnika:</span><span class="sxs-lookup"><span data-stu-id="f69e2-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="f69e2-132">Zawartość `<NotFound>` tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="f69e2-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="f69e2-133">Aby zastosować domyślny układ do `NotFound` zawartości, zobacz. <xref:blazor/layouts></span><span class="sxs-lookup"><span data-stu-id="f69e2-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="f69e2-134">Kierowanie do składników z wielu zestawów</span><span class="sxs-lookup"><span data-stu-id="f69e2-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="f69e2-135">Użyj parametru `AdditionalAssemblies` , aby określić dodatkowe zestawy dla `Router` składnika do uwzględnienia podczas wyszukiwania składników routingu.</span><span class="sxs-lookup"><span data-stu-id="f69e2-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="f69e2-136">Określone zestawy są traktowane jako uzupełnienie `AppAssembly`określonego zestawu.</span><span class="sxs-lookup"><span data-stu-id="f69e2-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="f69e2-137">W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w bibliotece klas, do której się odwołuje.</span><span class="sxs-lookup"><span data-stu-id="f69e2-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="f69e2-138">W poniższym `AdditionalAssemblies` przykładzie przedstawiono obsługę routingu dla `Component1`:</span><span class="sxs-lookup"><span data-stu-id="f69e2-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="f69e2-139">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="f69e2-139">Route parameters</span></span>

<span data-ttu-id="f69e2-140">Router używa parametrów trasy do wypełniania odpowiednich parametrów składnika o tej samej nazwie (bez uwzględniania wielkości liter):</span><span class="sxs-lookup"><span data-stu-id="f69e2-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="f69e2-141">Parametry opcjonalne nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f69e2-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="f69e2-142">W `@page` poprzednim przykładzie są stosowane dwie dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="f69e2-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="f69e2-143">Pierwszy zezwala na nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="f69e2-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="f69e2-144">Druga `@page` dyrektywa przyjmuje parametr `{text}` Route i przypisuje wartość do `Text` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f69e2-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="f69e2-145">Ograniczenia trasy</span><span class="sxs-lookup"><span data-stu-id="f69e2-145">Route constraints</span></span>

<span data-ttu-id="f69e2-146">Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.</span><span class="sxs-lookup"><span data-stu-id="f69e2-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="f69e2-147">W poniższym przykładzie trasa do `Users` składnika dopasowuje się tylko wtedy, gdy:</span><span class="sxs-lookup"><span data-stu-id="f69e2-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="f69e2-148">Segment `Id` trasy jest obecny w adresie URL żądania.</span><span class="sxs-lookup"><span data-stu-id="f69e2-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="f69e2-149">`Id` Segment jest liczbą całkowitą (`int`).</span><span class="sxs-lookup"><span data-stu-id="f69e2-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="f69e2-150">Dostępne są ograniczenia trasy podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="f69e2-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="f69e2-151">W przypadku ograniczeń trasy, które pasują do niezmiennej kultury, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="f69e2-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="f69e2-152">Typu</span><span class="sxs-lookup"><span data-stu-id="f69e2-152">Constraint</span></span> | <span data-ttu-id="f69e2-153">Przykład</span><span class="sxs-lookup"><span data-stu-id="f69e2-153">Example</span></span>           | <span data-ttu-id="f69e2-154">Przykładowe dopasowania</span><span class="sxs-lookup"><span data-stu-id="f69e2-154">Example Matches</span></span>                                                                  | <span data-ttu-id="f69e2-155">Niezmiennej</span><span class="sxs-lookup"><span data-stu-id="f69e2-155">Invariant</span></span><br><span data-ttu-id="f69e2-156">kultura</span><span class="sxs-lookup"><span data-stu-id="f69e2-156">culture</span></span><br><span data-ttu-id="f69e2-157">parowanie</span><span class="sxs-lookup"><span data-stu-id="f69e2-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="f69e2-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="f69e2-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="f69e2-159">Nie</span><span class="sxs-lookup"><span data-stu-id="f69e2-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="f69e2-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="f69e2-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="f69e2-161">Tak</span><span class="sxs-lookup"><span data-stu-id="f69e2-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="f69e2-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="f69e2-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="f69e2-163">Tak</span><span class="sxs-lookup"><span data-stu-id="f69e2-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="f69e2-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="f69e2-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="f69e2-165">Tak</span><span class="sxs-lookup"><span data-stu-id="f69e2-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="f69e2-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="f69e2-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="f69e2-167">Tak</span><span class="sxs-lookup"><span data-stu-id="f69e2-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="f69e2-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="f69e2-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="f69e2-169">Nie</span><span class="sxs-lookup"><span data-stu-id="f69e2-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="f69e2-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="f69e2-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="f69e2-171">Tak</span><span class="sxs-lookup"><span data-stu-id="f69e2-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="f69e2-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="f69e2-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="f69e2-173">Tak</span><span class="sxs-lookup"><span data-stu-id="f69e2-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="f69e2-174">Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie `int` jak `DateTime`lub), zawsze używają niezmiennej kultury.</span><span class="sxs-lookup"><span data-stu-id="f69e2-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="f69e2-175">W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.</span><span class="sxs-lookup"><span data-stu-id="f69e2-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="f69e2-176">Routing z adresami URL zawierającymi kropki</span><span class="sxs-lookup"><span data-stu-id="f69e2-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="f69e2-177">W aplikacjach serwera Blazor domyślną trasą w *_Host. cshtml* jest `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="f69e2-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="f69e2-178">Adres URL żądania, który zawiera kropkę`.`() nie pasuje do trasy domyślnej, ponieważ adres URL wygląda na żądanie pliku.</span><span class="sxs-lookup"><span data-stu-id="f69e2-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="f69e2-179">Aplikacja Blazor zwraca *404 — nie odnaleziono* odpowiedzi dla pliku statycznego, który nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="f69e2-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="f69e2-180">Aby użyć tras zawierających kropkę, skonfiguruj *_Host. cshtml* przy użyciu następującego szablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="f69e2-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="f69e2-181">`"/{**path}"` Szablon zawiera:</span><span class="sxs-lookup"><span data-stu-id="f69e2-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="f69e2-182">Podwójna gwiazdka *catch-all* (`**`) do przechwytywania ścieżki między wieloma granicami folderów bez kodowania ukośników (`/`).</span><span class="sxs-lookup"><span data-stu-id="f69e2-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="f69e2-183">`path`Nazwa parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="f69e2-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="f69e2-184">Składnia *catch-all* parametru (`*`/`**`) **nie** jest obsługiwana w składnikach Razor (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="f69e2-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="f69e2-185">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="f69e2-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="f69e2-186">Składnik NavLink</span><span class="sxs-lookup"><span data-stu-id="f69e2-186">NavLink component</span></span>

<span data-ttu-id="f69e2-187">Podczas tworzenia `NavLink` linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML`<a>`().</span><span class="sxs-lookup"><span data-stu-id="f69e2-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="f69e2-188">Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia klasy `active` CSS w zależności od tego, czy `href` jest on zgodny z bieżącym adresem URL. `NavLink`</span><span class="sxs-lookup"><span data-stu-id="f69e2-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="f69e2-189">`active` Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f69e2-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="f69e2-190">Poniższy `NavMenu` składnik tworzy pasek nawigacyjny [Bootstrap](https://getbootstrap.com/docs/) , który pokazuje, jak używać `NavLink` składników:</span><span class="sxs-lookup"><span data-stu-id="f69e2-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="f69e2-191">Dostępne są dwie `NavLinkMatch` opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="f69e2-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="f69e2-192">`NavLinkMatch.All`&ndash; Jest `NavLink` aktywny, gdy jest zgodny z całym bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="f69e2-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="f69e2-193">`NavLinkMatch.Prefix`(*ustawienie domyślne*) &ndash; Jest `NavLink` aktywny, gdy pasuje do dowolnego prefiksu bieżącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="f69e2-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="f69e2-194">W poprzednim przykładzie Strona główna `NavLink` `href=""` odpowiada głównemu adresowi URL i odbiera tylko klasy `active` CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="f69e2-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="f69e2-195">Druga `NavLink` otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `MyComponent` prefiksem (na przykład `https://localhost:5001/MyComponent` i `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="f69e2-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="f69e2-196">Dodatkowe `NavLink` atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="f69e2-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="f69e2-197">W poniższym przykładzie `NavLink` składnik zawiera `target` atrybut:</span><span class="sxs-lookup"><span data-stu-id="f69e2-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="f69e2-198">Renderuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="f69e2-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="f69e2-199">Pomoc dotycząca stanu identyfikatora URI i nawigacji</span><span class="sxs-lookup"><span data-stu-id="f69e2-199">URI and navigation state helpers</span></span>

<span data-ttu-id="f69e2-200">Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacją w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="f69e2-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="f69e2-201">`NavigationManager`zawiera zdarzenie i metody przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="f69e2-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="f69e2-202">Członek</span><span class="sxs-lookup"><span data-stu-id="f69e2-202">Member</span></span> | <span data-ttu-id="f69e2-203">Opis</span><span class="sxs-lookup"><span data-stu-id="f69e2-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="f69e2-204">Adresu</span><span class="sxs-lookup"><span data-stu-id="f69e2-204">Uri</span></span> | <span data-ttu-id="f69e2-205">Pobiera bieżący bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="f69e2-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="f69e2-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="f69e2-206">BaseUri</span></span> | <span data-ttu-id="f69e2-207">Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="f69e2-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="f69e2-208">`BaseUri` Zazwyczaj `href` odpowiada atrybutowi `<base>` elementu dokumentu w *wwwroot/index.html* (Blazor webassembly) lub *Pages/_Host. cshtml* (Blazor serwer).</span><span class="sxs-lookup"><span data-stu-id="f69e2-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="f69e2-209">Typu NavigateTo</span><span class="sxs-lookup"><span data-stu-id="f69e2-209">NavigateTo</span></span> | <span data-ttu-id="f69e2-210">Przechodzi do określonego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="f69e2-210">Navigates to the specified URI.</span></span> <span data-ttu-id="f69e2-211">Jeśli `forceLoad` jest `true`:</span><span class="sxs-lookup"><span data-stu-id="f69e2-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="f69e2-212">Routing po stronie klienta jest pomijany.</span><span class="sxs-lookup"><span data-stu-id="f69e2-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="f69e2-213">W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="f69e2-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="f69e2-214">LocationChanged</span><span class="sxs-lookup"><span data-stu-id="f69e2-214">LocationChanged</span></span> | <span data-ttu-id="f69e2-215">Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f69e2-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="f69e2-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="f69e2-216">ToAbsoluteUri</span></span> | <span data-ttu-id="f69e2-217">Konwertuje względny identyfikator URI na bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="f69e2-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="f69e2-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="f69e2-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="f69e2-219">Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej `GetBaseUri`przez), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI.</span><span class="sxs-lookup"><span data-stu-id="f69e2-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="f69e2-220">Poniższy składnik przechodzi do `Counter` składnika aplikacji po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="f69e2-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="f69e2-221">Poniższy składnik obsługuje zdarzenie zmiany lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="f69e2-221">The following component handles a location changed event.</span></span> <span data-ttu-id="f69e2-222">`HandleLocationChanged` Metoda jest odłączana, gdy `Dispose` jest wywoływana przez platformę.</span><span class="sxs-lookup"><span data-stu-id="f69e2-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="f69e2-223">Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.</span><span class="sxs-lookup"><span data-stu-id="f69e2-223">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="f69e2-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>zawiera następujące informacje o zdarzeniu:</span><span class="sxs-lookup"><span data-stu-id="f69e2-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="f69e2-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; Adres URL nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="f69e2-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="f69e2-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; `true`Jeśli Blazor przechwycono nawigację z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f69e2-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="f69e2-227">Jeśli `false`, element [nawigacyjny. typu NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) spowodował wystąpienie nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f69e2-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="f69e2-228">Aby uzyskać więcej informacji na temat usuwania składników <xref:blazor/lifecycle#component-disposal-with-idisposable>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="f69e2-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
