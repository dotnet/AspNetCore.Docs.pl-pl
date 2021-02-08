---
title: BlazorRouting ASP.NET Core
author: guardrex
description: Dowiedz się, jak zarządzać routingiem żądań w aplikacjach oraz jak używać składnika NavLink w Blazor aplikacjach do nawigacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: c53263ea48bc2900fd811f6e159aca97cdff26f7
ms.sourcegitcommit: 50d3e939a90c5480df480f651dda032901468dd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819032"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="d9190-103">BlazorRouting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d9190-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="d9190-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d9190-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d9190-105">W tym artykule dowiesz się, jak zarządzać routingiem żądań oraz jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnika do tworzenia linków nawigacji w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="d9190-105">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="d9190-106">Szablony tras</span><span class="sxs-lookup"><span data-stu-id="d9190-106">Route templates</span></span>

<span data-ttu-id="d9190-107"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik włącza Routing do Razor składników w Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d9190-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="d9190-108"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik jest używany w `App` składniku Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d9190-108">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="d9190-109">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-109">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

<span data-ttu-id="d9190-110">Gdy Razor składnik ( `.razor` ) z [ `@page` dyrektywą](xref:mvc/views/razor#page) jest kompilowany, wygenerowana Klasa składnika jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy składnika.</span><span class="sxs-lookup"><span data-stu-id="d9190-110">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="d9190-111">Po uruchomieniu aplikacji zestaw określony jako router `AppAssembly` jest skanowany w celu zebrania informacji o trasie dla składników aplikacji, które mają <xref:Microsoft.AspNetCore.Components.RouteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="d9190-111">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="d9190-112">W środowisku uruchomieniowym <xref:Microsoft.AspNetCore.Components.RouteView> składnik:</span><span class="sxs-lookup"><span data-stu-id="d9190-112">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="d9190-113">Odbiera <xref:Microsoft.AspNetCore.Components.RouteData> od siebie wraz <xref:Microsoft.AspNetCore.Components.Routing.Router> z dowolnymi parametrami trasy.</span><span class="sxs-lookup"><span data-stu-id="d9190-113">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="d9190-114">Renderuje określony składnik przy użyciu [układu](xref:blazor/layouts), w tym wszelkich dalszych zagnieżdżonych układów.</span><span class="sxs-lookup"><span data-stu-id="d9190-114">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="d9190-115">Opcjonalnie można określić <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr z klasą układu dla składników, które nie określają układu z [ `@layout` dyrektywą](xref:blazor/layouts#specify-a-layout-in-a-component).</span><span class="sxs-lookup"><span data-stu-id="d9190-115">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="d9190-116">BlazorSzablony projektu struktury określają `MainLayout` składnik ( `Shared/MainLayout.razor` ) jako domyślny układ aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d9190-116">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="d9190-117">Aby uzyskać więcej informacji na temat układów, zobacz <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="d9190-117">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="d9190-118">Składniki obsługują wiele szablonów tras przy użyciu wielu [ `@page` dyrektyw](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="d9190-118">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="d9190-119">Poniższy przykładowy składnik ładuje się na żądania dla `/BlazorRoute` i `/DifferentBlazorRoute` .</span><span class="sxs-lookup"><span data-stu-id="d9190-119">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="d9190-120">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-120">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="d9190-121">Aby adresy URL zostały poprawnie rozpoznane, aplikacja musi zawierać `<base>` tag w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ) z ścieżką bazową aplikacji określoną w `href` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="d9190-121">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="d9190-122">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="d9190-122">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="d9190-123">Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości</span><span class="sxs-lookup"><span data-stu-id="d9190-123">Provide custom content when content isn't found</span></span>

<span data-ttu-id="d9190-124"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.</span><span class="sxs-lookup"><span data-stu-id="d9190-124">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="d9190-125">W `App` składniku Ustaw zawartość niestandardową w <xref:Microsoft.AspNetCore.Components.Routing.Router> szablonie składnika <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .</span><span class="sxs-lookup"><span data-stu-id="d9190-125">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="d9190-126">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-126">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="d9190-127">Dowolne elementy są obsługiwane jako zawartość `<NotFound>` tagów, takich jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="d9190-127">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="d9190-128">Aby zastosować domyślny układ do <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> zawartości, zobacz <xref:blazor/layouts#default-layout> .</span><span class="sxs-lookup"><span data-stu-id="d9190-128">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="d9190-129">Kierowanie do składników z wielu zestawów</span><span class="sxs-lookup"><span data-stu-id="d9190-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="d9190-130">Użyj <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametru, aby określić dodatkowe zestawy dla <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika do uwzględnienia podczas wyszukiwania składników routingu.</span><span class="sxs-lookup"><span data-stu-id="d9190-130">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="d9190-131">Dodatkowe zestawy są skanowane oprócz zestawu określonego dla `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="d9190-131">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="d9190-132">W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w [bibliotece klas składników](xref:blazor/components/class-libraries), do której się odwołuje.</span><span class="sxs-lookup"><span data-stu-id="d9190-132">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="d9190-133">W poniższym <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> przykładzie przedstawiono obsługę routingu dla programu `Component1` .</span><span class="sxs-lookup"><span data-stu-id="d9190-133">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="d9190-134">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-134">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="d9190-135">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="d9190-135">Route parameters</span></span>

<span data-ttu-id="d9190-136">Router używa parametrów trasy do wypełniania odpowiednich [parametrów składnika](xref:blazor/components/index#component-parameters) o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="d9190-136">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="d9190-137">W nazwach parametrów trasy jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="d9190-137">Route parameter names are case insensitive.</span></span> <span data-ttu-id="d9190-138">W poniższym przykładzie `text` parametr przypisuje wartość segmentu trasy do `Text` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="d9190-138">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="d9190-139">Gdy jest wykonywane żądanie `/RouteParameter/amazing` , `<h1>` zawartość tagu jest renderowana jako `Blazor is amazing!` .</span><span class="sxs-lookup"><span data-stu-id="d9190-139">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="d9190-140">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-140">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d9190-141">Parametry opcjonalne są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="d9190-141">Optional parameters are supported.</span></span> <span data-ttu-id="d9190-142">W poniższym przykładzie `text` opcjonalny parametr przypisuje wartość segmentu trasy do `Text` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="d9190-142">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="d9190-143">Jeśli segment nie istnieje, wartość `Text` jest ustawiana na `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="d9190-143">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="d9190-144">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-144">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d9190-145">Parametry opcjonalne nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="d9190-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="d9190-146">W poniższym przykładzie są stosowane dwie [ `@page` dyrektywy](xref:mvc/views/razor#page) .</span><span class="sxs-lookup"><span data-stu-id="d9190-146">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="d9190-147">Pierwsza dyrektywa umożliwia nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="d9190-147">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d9190-148">Druga dyrektywa przypisuje `{text}` wartość parametru trasy do `Text` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="d9190-148">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="d9190-149">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-149">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="d9190-150">Użyj [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) zamiast, [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) Aby zezwolić aplikacji na nawigowanie do tego samego składnika przy użyciu innej opcjonalnej wartości parametru.</span><span class="sxs-lookup"><span data-stu-id="d9190-150">Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="d9190-151">Na podstawie powyższego przykładu należy użyć, `OnParametersSet` gdy użytkownik powinien przejść od `/RouteParameter` do `/RouteParameter/amazing` lub z `/RouteParameter/amazing` do `/RouteParameter` :</span><span class="sxs-lookup"><span data-stu-id="d9190-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="d9190-152">Ograniczenia trasy</span><span class="sxs-lookup"><span data-stu-id="d9190-152">Route constraints</span></span>

<span data-ttu-id="d9190-153">Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.</span><span class="sxs-lookup"><span data-stu-id="d9190-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="d9190-154">W poniższym przykładzie trasa do `User` składnika dopasowuje się tylko wtedy, gdy:</span><span class="sxs-lookup"><span data-stu-id="d9190-154">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="d9190-155">`Id`Segment trasy jest obecny w adresie URL żądania.</span><span class="sxs-lookup"><span data-stu-id="d9190-155">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="d9190-156">`Id`Segment jest typu Integer ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="d9190-156">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="d9190-157">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-157">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="d9190-158">Dostępne są ograniczenia trasy podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="d9190-158">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="d9190-159">W przypadku ograniczeń trasy, które pasują do niezmiennej kultury, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="d9190-159">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="d9190-160">Typu</span><span class="sxs-lookup"><span data-stu-id="d9190-160">Constraint</span></span> | <span data-ttu-id="d9190-161">Przykład</span><span class="sxs-lookup"><span data-stu-id="d9190-161">Example</span></span>           | <span data-ttu-id="d9190-162">Przykładowe dopasowania</span><span class="sxs-lookup"><span data-stu-id="d9190-162">Example Matches</span></span>                                                                  | <span data-ttu-id="d9190-163">Niezmiennej</span><span class="sxs-lookup"><span data-stu-id="d9190-163">Invariant</span></span><br><span data-ttu-id="d9190-164">kultura</span><span class="sxs-lookup"><span data-stu-id="d9190-164">culture</span></span><br><span data-ttu-id="d9190-165">parowanie</span><span class="sxs-lookup"><span data-stu-id="d9190-165">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="d9190-166">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="d9190-166">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="d9190-167">Nie</span><span class="sxs-lookup"><span data-stu-id="d9190-167">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="d9190-168">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="d9190-168">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="d9190-169">Tak</span><span class="sxs-lookup"><span data-stu-id="d9190-169">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="d9190-170">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="d9190-170">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="d9190-171">Tak</span><span class="sxs-lookup"><span data-stu-id="d9190-171">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="d9190-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d9190-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d9190-173">Tak</span><span class="sxs-lookup"><span data-stu-id="d9190-173">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="d9190-174">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d9190-174">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d9190-175">Tak</span><span class="sxs-lookup"><span data-stu-id="d9190-175">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="d9190-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="d9190-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="d9190-177">Nie</span><span class="sxs-lookup"><span data-stu-id="d9190-177">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="d9190-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d9190-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d9190-179">Tak</span><span class="sxs-lookup"><span data-stu-id="d9190-179">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="d9190-180">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d9190-180">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d9190-181">Tak</span><span class="sxs-lookup"><span data-stu-id="d9190-181">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="d9190-182">Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub <xref:System.DateTime> ), zawsze używają niezmiennej kultury.</span><span class="sxs-lookup"><span data-stu-id="d9190-182">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="d9190-183">W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.</span><span class="sxs-lookup"><span data-stu-id="d9190-183">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="d9190-184">Routing z adresami URL zawierającymi kropki</span><span class="sxs-lookup"><span data-stu-id="d9190-184">Routing with URLs that contain dots</span></span>

<span data-ttu-id="d9190-185">W przypadku hostowanych Blazor WebAssembly i Blazor Server aplikacji szablon trasy domyślnej po stronie serwera zakłada, że jeśli ostatni segment adresu URL żądania zawiera kropkę ( `.` ), że plik jest żądany.</span><span class="sxs-lookup"><span data-stu-id="d9190-185">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="d9190-186">Na przykład adres URL `https://localhost.com:5001/example/some.thing` jest interpretowany przez router jako żądanie pliku o nazwie `some.thing` .</span><span class="sxs-lookup"><span data-stu-id="d9190-186">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="d9190-187">Bez dodatkowej konfiguracji aplikacja zwraca *Nieznalezioną odpowiedź 404* , jeśli `some.thing` była przeznaczona do skierowania do składnika z [ `@page` dyrektywą](xref:mvc/views/razor#page) i `some.thing` jest wartością parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="d9190-187">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="d9190-188">Aby użyć trasy z co najmniej jednym parametrem zawierającym kropkę, aplikacja musi skonfigurować trasę z szablonem niestandardowym.</span><span class="sxs-lookup"><span data-stu-id="d9190-188">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="d9190-189">Rozważmy poniższy `Example` składnik, który może odbierać parametr trasy z ostatniego segmentu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="d9190-189">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="d9190-190">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-190">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="d9190-191">Aby zezwolić *`Server`* aplikacji hostowanego Blazor WebAssembly rozwiązania na kierowanie żądania z kropką w `param` parametrze trasy, należy dodać szablon "rezerwowy" trasy pliku z opcjonalnym parametrem w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d9190-191">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="d9190-192">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="d9190-192">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="d9190-193">Aby skonfigurować Blazor Server aplikację do kierowania żądania z kropką w `param` parametrze trasy, Dodaj szablon trasy strony rezerwowej z opcjonalnym parametrem w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d9190-193">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="d9190-194">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="d9190-194">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="d9190-195">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="d9190-195">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="d9190-196">Catch-wszystkie parametry tras</span><span class="sxs-lookup"><span data-stu-id="d9190-196">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d9190-197">Wszystkie parametry trasy, które przechwytują ścieżki między wieloma granicami folderów, są obsługiwane w składnikach.</span><span class="sxs-lookup"><span data-stu-id="d9190-197">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="d9190-198">Wszystkie parametry trasy są następujące:</span><span class="sxs-lookup"><span data-stu-id="d9190-198">Catch-all route parameters are:</span></span>

* <span data-ttu-id="d9190-199">Nazwa jest zgodna z nazwą segmentu trasy.</span><span class="sxs-lookup"><span data-stu-id="d9190-199">Named to match the route segment name.</span></span> <span data-ttu-id="d9190-200">W nazewnictwie nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="d9190-200">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="d9190-201">`string`Typ.</span><span class="sxs-lookup"><span data-stu-id="d9190-201">A `string` type.</span></span> <span data-ttu-id="d9190-202">Struktura nie zapewnia automatycznego rzutowania.</span><span class="sxs-lookup"><span data-stu-id="d9190-202">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="d9190-203">Na końcu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="d9190-203">At the end of the URL.</span></span>

<span data-ttu-id="d9190-204">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-204">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

<span data-ttu-id="d9190-205">Dla adresu URL `/catch-all/this/is/a/test` z szablonem trasy dla `/catch-all/{*pageRoute}` , wartość `PageRoute` jest ustawiona na `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="d9190-205">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="d9190-206">Ukośniki i segmenty ścieżki przechwyconej są zdekodowane.</span><span class="sxs-lookup"><span data-stu-id="d9190-206">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="d9190-207">Dla szablonu trasy w programie `/catch-all/{*pageRoute}` adres URL `/catch-all/this/is/a%2Ftest%2A` daje wartość `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="d9190-207">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d9190-208">Wszystkie parametry tras są obsługiwane w ASP.NET Core 5,0 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="d9190-208">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="d9190-209">Aby uzyskać więcej informacji, wybierz wersję 5,0 tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="d9190-209">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="d9190-210">Pomoc dotycząca stanu identyfikatora URI i nawigacji</span><span class="sxs-lookup"><span data-stu-id="d9190-210">URI and navigation state helpers</span></span>

<span data-ttu-id="d9190-211">Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do zarządzania identyfikatorami URI i nawigacją w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="d9190-211">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="d9190-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> zawiera zdarzenie i metody przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="d9190-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="d9190-213">Członek</span><span class="sxs-lookup"><span data-stu-id="d9190-213">Member</span></span> | <span data-ttu-id="d9190-214">Opis</span><span class="sxs-lookup"><span data-stu-id="d9190-214">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="d9190-215">Pobiera bieżący bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="d9190-215">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="d9190-216">Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="d9190-216">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="d9190-217">Zazwyczaj <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpowiada `href` atrybutowi `<base>` elementu dokumentu w `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="d9190-217">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="d9190-218">Przechodzi do określonego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="d9190-218">Navigates to the specified URI.</span></span> <span data-ttu-id="d9190-219">Jeśli `forceLoad` jest `true` :</span><span class="sxs-lookup"><span data-stu-id="d9190-219">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="d9190-220">Routing po stronie klienta jest pomijany.</span><span class="sxs-lookup"><span data-stu-id="d9190-220">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="d9190-221">W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="d9190-221">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="d9190-222">Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji.</span><span class="sxs-lookup"><span data-stu-id="d9190-222">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="d9190-223">Konwertuje względny identyfikator URI na bezwzględny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="d9190-223">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="d9190-224">Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej przez <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI.</span><span class="sxs-lookup"><span data-stu-id="d9190-224">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="d9190-225">W przypadku <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> zdarzenia program <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> dostarcza następujące informacje dotyczące zdarzeń nawigacyjnych:</span><span class="sxs-lookup"><span data-stu-id="d9190-225">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="d9190-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adres URL nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="d9190-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="d9190-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Jeśli `true` Blazor przechwytuje nawigację z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="d9190-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="d9190-228">Jeśli `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wystąpiła nawigacja.</span><span class="sxs-lookup"><span data-stu-id="d9190-228">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="d9190-229">Następujący składnik:</span><span class="sxs-lookup"><span data-stu-id="d9190-229">The following component:</span></span>

* <span data-ttu-id="d9190-230">Przechodzi do `Counter` składnika aplikacji ( `Pages/Counter.razor` ), gdy przycisk jest wybierany przy użyciu <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .</span><span class="sxs-lookup"><span data-stu-id="d9190-230">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="d9190-231">Obsługuje zdarzenie zmiany lokalizacji przez subskrybowanie do <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d9190-231">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="d9190-232">Metoda jest odłączana, `HandleLocationChanged` gdy `Dispose` jest wywoływana przez platformę.</span><span class="sxs-lookup"><span data-stu-id="d9190-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="d9190-233">Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.</span><span class="sxs-lookup"><span data-stu-id="d9190-233">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="d9190-234">Implementacja rejestratora rejestruje następujące informacje po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="d9190-234">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="d9190-235">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-235">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

<span data-ttu-id="d9190-236">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="d9190-236">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="d9190-237">Ciąg zapytania i parametry analizy</span><span class="sxs-lookup"><span data-stu-id="d9190-237">Query string and parse parameters</span></span>

<span data-ttu-id="d9190-238">Ciąg zapytania żądania jest uzyskiwany z <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="d9190-238">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="d9190-239">Aby przeanalizować parametry ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="d9190-239">To parse a query string's parameters:</span></span>

* <span data-ttu-id="d9190-240">Aplikacja może korzystać z <xref:Microsoft.AspNetCore.WebUtilities> interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="d9190-240">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="d9190-241">Jeśli interfejs API nie jest dostępny dla aplikacji, Dodaj odwołanie do pakietu w pliku projektu aplikacji dla [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="d9190-241">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="d9190-242">Uzyskaj wartość po przeanalizowaniu ciągu zapytania za pomocą elementu <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d9190-242">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="d9190-243">Poniższy `ParseQueryString` przykład składnika analizuje kod parametru ciągu zapytania o nazwie `ship` .</span><span class="sxs-lookup"><span data-stu-id="d9190-243">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="d9190-244">Na przykład para klucz-wartość ciągu zapytania adresu URL `?ship=Tardis` przechwytuje wartość `Tardis` w `queryValue` .</span><span class="sxs-lookup"><span data-stu-id="d9190-244">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="d9190-245">W poniższym przykładzie przejdź do aplikacji przy użyciu adresu URL `https://localhost:5001/parse-query-string?ship=Tardis` .</span><span class="sxs-lookup"><span data-stu-id="d9190-245">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="d9190-246">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9190-246">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="d9190-247">`NavLink` i `NavMenu` składniki</span><span class="sxs-lookup"><span data-stu-id="d9190-247">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="d9190-248"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Podczas tworzenia linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML ( `<a>` ).</span><span class="sxs-lookup"><span data-stu-id="d9190-248">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="d9190-249"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia `active` klasy CSS w zależności od tego, czy jest on `href` zgodny z bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="d9190-249">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="d9190-250">`active`Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="d9190-250">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="d9190-251">Opcjonalnie Przypisz nazwę klasy CSS do, aby <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> zastosować niestandardową klasę CSS do renderowanego łącza, gdy bieżąca trasa jest zgodna z `href` .</span><span class="sxs-lookup"><span data-stu-id="d9190-251">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="d9190-252">Poniższy `NavMenu` składnik tworzy [`Bootstrap`](https://getbootstrap.com/docs/) pasek nawigacyjny, który pokazuje, jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składników:</span><span class="sxs-lookup"><span data-stu-id="d9190-252">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="d9190-253">`NavMenu`Składnik ( `NavMenu.razor` ) znajduje się w `Shared` folderze aplikacji wygenerowanej na podstawie Blazor szablonów projektu.</span><span class="sxs-lookup"><span data-stu-id="d9190-253">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="d9190-254">Dostępne są dwie <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="d9190-254">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="d9190-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Jest aktywny, gdy jest zgodny z całym bieżącym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="d9190-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="d9190-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*ustawienie domyślne*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> jest aktywny, gdy pasuje do dowolnego PREFIKSU bieżącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="d9190-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="d9190-257">W poprzednim przykładzie Strona główna <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` odpowiada GŁÓWNEmu adresowi URL i odbiera tylko `active` klasy CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="d9190-257">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="d9190-258">Druga <xref:Microsoft.AspNetCore.Components.Routing.NavLink> otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `component` prefiksem (na przykład `https://localhost:5001/component` i `https://localhost:5001/component/another-segment` ).</span><span class="sxs-lookup"><span data-stu-id="d9190-258">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="d9190-259">Dodatkowe <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="d9190-259">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="d9190-260">W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnik zawiera `target` atrybut:</span><span class="sxs-lookup"><span data-stu-id="d9190-260">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="d9190-261">Renderuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="d9190-261">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="d9190-262">Ze względu na sposób, który Blazor renderuje zawartość podrzędną, `NavLink` składniki renderowania wewnątrz `for` pętli wymagają zmiennej lokalnego indeksu, jeśli zmienna pętli zwiększania jest używana w `NavLink` zawartości składnika (potomne):</span><span class="sxs-lookup"><span data-stu-id="d9190-262">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="d9190-263">Użycie zmiennej index w tym scenariuszu jest wymaganiem dla **dowolnego** składnika podrzędnego, który używa zmiennej pętli w jej [zawartości podrzędnej](xref:blazor/components/index#child-content), a nie tylko `NavLink` składnika.</span><span class="sxs-lookup"><span data-stu-id="d9190-263">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="d9190-264">Alternatywnie możesz użyć `foreach` pętli z <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="d9190-264">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="d9190-265">ASP.NET Core integracja z routingiem punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="d9190-265">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="d9190-266">*Ta sekcja dotyczy tylko Blazor Server aplikacji.*</span><span class="sxs-lookup"><span data-stu-id="d9190-266">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="d9190-267">Blazor Server Program jest zintegrowany z [ASP.NET Core routingiem punktów końcowych](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="d9190-267">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="d9190-268">Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za pomocą <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> programu w programie `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d9190-268">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="d9190-269">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="d9190-269">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="d9190-270">Typowa konfiguracja polega na kierowaniu wszystkich żądań do Razor strony, które pełnią rolę hosta dla części aplikacji po stronie serwera Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="d9190-270">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="d9190-271">Zgodnie z Konwencją strona *hosta* jest zazwyczaj nazywana `_Host.cshtml` w `Pages` folderze aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d9190-271">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="d9190-272">Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras.</span><span class="sxs-lookup"><span data-stu-id="d9190-272">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="d9190-273">Trasa rezerwowa jest używana, gdy inne trasy nie są zgodne.</span><span class="sxs-lookup"><span data-stu-id="d9190-273">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="d9190-274">Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania routingu składników w Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d9190-274">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="d9190-275">Aby uzyskać informacje na temat konfigurowania <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> dla hosta niebędącego głównym adresem URL, zobacz <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="d9190-275">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
