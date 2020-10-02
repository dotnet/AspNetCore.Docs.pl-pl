---
title: BlazorWirtualizacja składników ASP.NET Core
author: guardrex
description: Dowiedz się, jak używać wirtualizacji składników w Blazor aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 2ba698eaba23fd67617375e5186d40d45d9772fd
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653883"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="d7daa-103">BlazorWirtualizacja składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7daa-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="d7daa-104">Autor [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d7daa-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="d7daa-105">Popraw postrzeganą wydajność renderowania składników przy użyciu Blazor wbudowanej obsługi wirtualizacji platformy.</span><span class="sxs-lookup"><span data-stu-id="d7daa-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="d7daa-106">Wirtualizacja jest techniką do ograniczania renderowania interfejsu użytkownika do zaledwie części, które są obecnie widoczne.</span><span class="sxs-lookup"><span data-stu-id="d7daa-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="d7daa-107">Na przykład wirtualizacja jest przydatna, gdy aplikacja musi renderować długą listę elementów, a tylko podzbiór elementów ma być widoczny w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="d7daa-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="d7daa-108">Blazor udostępnia `Virtualize` składnik, który może służyć do dodawania wirtualizacji do składników aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7daa-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d7daa-109">Bez wirtualizacji typowa lista może używać pętli języka C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) do renderowania każdego elementu na liście:</span><span class="sxs-lookup"><span data-stu-id="d7daa-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="d7daa-110">Jeśli lista zawiera tysiące elementów, renderowanie listy może zająć dużo czasu.</span><span class="sxs-lookup"><span data-stu-id="d7daa-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="d7daa-111">Użytkownik może napotkać zauważalne opóźnienie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d7daa-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="d7daa-112">Zamiast wyrenderować każdy element na liście wszystkie jednocześnie, Zastąp [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pętlę `Virtualize` składnikiem i określ stałe źródło elementu z `Items` .</span><span class="sxs-lookup"><span data-stu-id="d7daa-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="d7daa-113">Renderowane są tylko te elementy, które są obecnie widoczne:</span><span class="sxs-lookup"><span data-stu-id="d7daa-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="d7daa-114">Jeśli nie określisz kontekstu do składnika przy użyciu `Context` , użyj `context` wartości ( `@context.{PROPERTY}` ) w szablonie zawartości elementu:</span><span class="sxs-lookup"><span data-stu-id="d7daa-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="d7daa-115">`Virtualize`Składnik oblicza, ile elementów ma być renderowanych na podstawie wysokości kontenera i rozmiaru elementów renderowane.</span><span class="sxs-lookup"><span data-stu-id="d7daa-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="d7daa-116">Zawartość elementu `Virtualize` składnika może obejmować:</span><span class="sxs-lookup"><span data-stu-id="d7daa-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="d7daa-117">Zwykły kod HTML i Razor kodu, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="d7daa-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="d7daa-118">Co najmniej jeden Razor składnik.</span><span class="sxs-lookup"><span data-stu-id="d7daa-118">One or more Razor components.</span></span>
* <span data-ttu-id="d7daa-119">Mieszane składniki HTML/ Razor i Razor .</span><span class="sxs-lookup"><span data-stu-id="d7daa-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="d7daa-120">Delegat dostawcy elementów</span><span class="sxs-lookup"><span data-stu-id="d7daa-120">Item provider delegate</span></span>

<span data-ttu-id="d7daa-121">Jeśli nie chcesz ładować wszystkich elementów do pamięci, możesz określić metodę delegata dostawcy elementów dla parametru składnika, `ItemsProvider` który asynchronicznie pobiera żądane elementy na żądanie:</span><span class="sxs-lookup"><span data-stu-id="d7daa-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="d7daa-122">Dostawca elementów odbiera obiekt `ItemsProviderRequest` , który określa wymaganą liczbę elementów rozpoczynającą się określonym indeksem początkowym.</span><span class="sxs-lookup"><span data-stu-id="d7daa-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="d7daa-123">Następnie dostawca elementów pobiera żądane elementy z bazy danych lub innej usługi i zwraca je `ItemsProviderResult<TItem>` wraz z liczbą całkowitej liczby elementów.</span><span class="sxs-lookup"><span data-stu-id="d7daa-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="d7daa-124">Dostawca elementów może wybrać pobieranie elementów z każdym żądaniem lub buforowanie ich w taki sposób, aby były one łatwo dostępne.</span><span class="sxs-lookup"><span data-stu-id="d7daa-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="d7daa-125">Nie próbuj używać dostawcy elementów i przypisz kolekcję do tego `Items` samego `Virtualize` składnika.</span><span class="sxs-lookup"><span data-stu-id="d7daa-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="d7daa-126">Poniższy przykład ładuje pracowników z `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="d7daa-126">The following example loads employees from an `EmployeeService`:</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a><span data-ttu-id="d7daa-127">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="d7daa-127">Placeholder</span></span>

<span data-ttu-id="d7daa-128">Ponieważ żądanie elementów ze zdalnego źródła danych może zająć trochę czasu, możesz renderować symbol zastępczy ( `<Placeholder>...</Placeholder>` ), dopóki dane elementu nie będą dostępne:</span><span class="sxs-lookup"><span data-stu-id="d7daa-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="d7daa-129">Rozmiar elementu</span><span class="sxs-lookup"><span data-stu-id="d7daa-129">Item size</span></span>

<span data-ttu-id="d7daa-130">Można ustawić rozmiar każdego elementu w pikselach `ItemSize` (domyślnie: 50px):</span><span class="sxs-lookup"><span data-stu-id="d7daa-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="d7daa-131">Liczba przeskanowania</span><span class="sxs-lookup"><span data-stu-id="d7daa-131">Overscan count</span></span>

<span data-ttu-id="d7daa-132">`OverscanCount` Określa, ile dodatkowych elementów jest renderowanych przed i po widocznym regionie.</span><span class="sxs-lookup"><span data-stu-id="d7daa-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="d7daa-133">To ustawienie pomaga zmniejszyć częstotliwość renderowania podczas przewijania.</span><span class="sxs-lookup"><span data-stu-id="d7daa-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="d7daa-134">Jednak wyższe wartości powodują więcej elementów renderowanych na stronie (domyślnie: 3):</span><span class="sxs-lookup"><span data-stu-id="d7daa-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d7daa-135">Na przykład siatka lub lista, która renderuje setki wierszy zawierających składniki, ma intensywną obsługę procesora.</span><span class="sxs-lookup"><span data-stu-id="d7daa-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="d7daa-136">Rozważ wirtualizację układu siatki lub listy tak, że tylko podzestaw składników jest renderowany w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="d7daa-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span>

<span data-ttu-id="d7daa-137">Poniższy `Virtualize` składnik ( `Virtualize.cs` ) implementuje, <xref:Microsoft.AspNetCore.Components.ComponentBase> Aby renderować zawartość podrzędną na podstawie przewijania przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="d7daa-137">The following `Virtualize` component (`Virtualize.cs`) implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render child content based on user scrolling:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Rendering;
using Microsoft.JSInterop;

public class Virtualize<TItem> : ComponentBase
{
    [Parameter]
    public string TagName { get; set; } = "div";

    [Parameter]
    public RenderFragment<TItem> ChildContent { get; set; }

    [Parameter]
    public ICollection<TItem> Items { get; set; }

    [Parameter]
    public double ItemHeight { get; set; }

    [Parameter(CaptureUnmatchedValues = true)] 
    public Dictionary<string, object> Attributes { get; set; }

    [Inject]
    IJSRuntime JS { get; set; }

    ElementReference contentElement;
    int numItemsToSkipBefore;
    int numItemsToShow;

    protected override void BuildRenderTree(RenderTreeBuilder builder)
    {
        // Render actual content
        builder.OpenElement(0, TagName);
        builder.AddMultipleAttributes(1, Attributes);

        var translateY = numItemsToSkipBefore * ItemHeight;
        builder.AddAttribute(2, "style", $"transform: translateY({ translateY }px);");
        builder.AddAttribute(2, "data-translateY", translateY);
        builder.AddElementReferenceCapture(3, @ref => { contentElement = @ref; });

        // As an important optimization, *don't* use builder.AddContent(seq, ChildContent, item)
        // because that implicitly wraps a new region around each item, which in turn means that 
        // @key does nothing (because keys are scoped to regions). Instead, create a single 
        // container region and then invoke the fragments directly.

        builder.OpenRegion(4);

        foreach (var item in Items.Skip(numItemsToSkipBefore).Take(numItemsToShow))
        {
            ChildContent(item)(builder);
        }

        builder.CloseRegion();

        builder.CloseElement();

        // Also emit a spacer that causes the total vertical height to add up to 
        // Items.Count()*numItems

        builder.OpenElement(5, "div");
        var numHiddenItems = Items.Count - numItemsToShow;
        builder.AddAttribute(6, "style", 
            $"width: 1px; height: { numHiddenItems * ItemHeight }px;");
        builder.CloseElement();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            var objectRef = DotNetObjectReference.Create(this);
            var initResult = await JS.InvokeAsync<ScrollEventArgs>(
                "VirtualizedComponent._initialize", objectRef, contentElement);
            OnScroll(initResult);
        }
    }

    [JSInvokable]
    public void OnScroll(ScrollEventArgs args)
    {
        var relativeTop = args.ContainerRect.Top - args.ContentRect.Top;
        numItemsToSkipBefore = Math.Max(0, (int)(relativeTop / ItemHeight));

        var visibleHeight = args.ContainerRect.Bottom - 
            (args.ContentRect.Top + numItemsToSkipBefore * ItemHeight);
        numItemsToShow = (int)Math.Ceiling(visibleHeight / ItemHeight) + 3;

        StateHasChanged();
    }

    public class ScrollEventArgs
    {
        public DOMRect ContainerRect { get; set; }
        public DOMRect ContentRect { get; set; }
    }

    public class DOMRect
    {
        public double Top { get; set; }
        public double Bottom { get; set; }
        public double Left { get; set; } 
        public double Right { get; set; }
        public double Width { get; set; }
        public double Height { get; set; }
    }
}
```

<span data-ttu-id="d7daa-138">Poniższy `FetchData` składnik ( `FetchData.razor` ) używa poprzedniego `Virtualize` składnika do wyświetlania 25 wierszy danych pogody jednocześnie:</span><span class="sxs-lookup"><span data-stu-id="d7daa-138">The following `FetchData` component (`FetchData.razor`) uses the preceding `Virtualize` component to display 25 rows of weather data at a time:</span></span>

```razor
@page "/"
@page "/fetchdata"
@inject HttpClient Http

<h1>Weather forecast</h1>

<p>This component demonstrates fetching data from a service.</p>

@if (forecasts == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h2>Using <code>table-layout: fixed</code></h2>
    <div style="height:300px; overflow-y: auto;">
        <Virtualize ItemHeight="25" Items="@forecasts">
            <tr @key="@context.GetHashCode()" 
                    style="display: table; table-layout: fixed; width: 100%;">
                <td>@context.Date.ToShortDateString()</td>
                <td>@context.TemperatureC</td>
                <td>@context.TemperatureF</td>
                <td>@context.Summary</td>
            </tr>
        </Virtualize>
    </div>

    <h2>Using <code>position: sticky</code></h2>
    <div style="height: 300px; overflow-y: auto; position: relative;">
        <table>
            <thead class="sticky">
                <tr>
                    <th>Date</th>
                    <th>Temperature (C)</th>
                    <th>Temperature (F)</th>
                    <th>Summary</th>
                </tr>
            </thead>

            <Virtualize TagName="tbody" ItemHeight="25" Items="@forecasts">
                <tr @key="@context.GetHashCode()">
                    <td>@context.Date.ToShortDateString()</td>
                    <td>@context.TemperatureC</td>
                    <td>@context.TemperatureF</td>
                    <td>@context.Summary</td>
                </tr>
            </Virtualize>
        </table>
    </div>

    <style type="text/css">
        thead.sticky th {
            position: sticky;
            top: 0;
        }
        tr td {
            height: 25px;
        }
    </style>
}

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "sample-data/weather.json");
    }

    public class WeatherForecast
    {
        public DateTime Date { get; set; }

        public int TemperatureC { get; set; }

        public string Summary { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
    }
}
```

<span data-ttu-id="d7daa-139">W poprzednim przykładzie podejście polega na uniknięciu bezwzględnego pozycjonowania poszczególnych elementów.</span><span class="sxs-lookup"><span data-stu-id="d7daa-139">In the preceding example, the approach is about avoiding absolute positioning for each individual item.</span></span> <span data-ttu-id="d7daa-140">Pozycjonowanie bezwzględne może mieć pewne zalety (można upewnić się, że elementy zajmują określoną ilość miejsca na osi Y), ale w przypadku zmiany rozmiaru zawartości nie można pobrać kolumn tabeli w górę w wierszach/nagłówkach.</span><span class="sxs-lookup"><span data-stu-id="d7daa-140">Absolute positioning would have some advantages (we can be sure the items do take up the specified amount of Y space) but has the bad disadvantage that you lose the normal widths and can't get table columns to line up across rows/header when based on content sizing.</span></span>

<span data-ttu-id="d7daa-141">Pojęcie związane z projektem `Virtualize` składnika polega na tym, że składnik nie zmienia sposobu, w jaki elementy są określone w modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="d7daa-141">The concept behind the design of the `Virtualize` component is that the component doesn't change how the items are laid out within the DOM.</span></span> <span data-ttu-id="d7daa-142">Nie dodano żadnych elementów otoki, oprócz jednego z nich, którego `TagName` określisz.</span><span class="sxs-lookup"><span data-stu-id="d7daa-142">There are no added wrapper elements, besides the single one whose `TagName` you specify.</span></span>

<span data-ttu-id="d7daa-143">Najlepszym rozwiązaniem jest uniknięcie nawet `TagName` elementu otoki.</span><span class="sxs-lookup"><span data-stu-id="d7daa-143">The best approach is to avoid even the `TagName` wrapper element.</span></span> <span data-ttu-id="d7daa-144">`Virtualize`Składnik nie emituje żadnych elementów.</span><span class="sxs-lookup"><span data-stu-id="d7daa-144">Have the `Virtualize` component emit no elements of its own.</span></span> <span data-ttu-id="d7daa-145">Cały składnik jest renderowany, jednak wiele wystąpień szablonów jest wymaganych do wypełnienia dowolnego pozostałego widocznego miejsca w najbliższym elemencie nadrzędnym z możliwością przewijania, a następnie Dodaj następujący element, który umożliwia przewijanie elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="d7daa-145">All the component does is render however many of the template instances are required to fill up any remaining visible space in the closest scrollable ancestor, plus add a following spacer element that makes the scrollable ancestor have the right scrolling range.</span></span> <span data-ttu-id="d7daa-146">W odniesieniu do danego układu jest to tak samo jak w przypadku, gdy pełen zakres elementów podrzędnych był fizycznie w modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="d7daa-146">As far as the layout is concerned, it's the same as if the full range of children were physically in the DOM.</span></span> <span data-ttu-id="d7daa-147">Wymaga podania dokładnej precyzji `ItemHeight` .</span><span class="sxs-lookup"><span data-stu-id="d7daa-147">It does require you to specify an accurate `ItemHeight` though.</span></span> <span data-ttu-id="d7daa-148">Jeśli okaże się to niewłaściwe (na przykład dlatego, że masz wątpliwości i uważasz, że jest on prawidłowy, aby określić `style.height` w przypadku `<tr>` ), składnik zostanie uruchomiony w celu renderowania nieprawidłowej liczby wystąpień szablonu i nie wypełnia interfejsu użytkownika ani niewydajnego renderowania za dużo.</span><span class="sxs-lookup"><span data-stu-id="d7daa-148">If you get it wrong (for example, because you're confused and think it's valid to specify `style.height` on a `<tr>`), then the component ends up rendering the wrong number of template instances and either not fill up the UI or inefficiently render too many.</span></span> <span data-ttu-id="d7daa-149">Ponadto zakres przewijania w elemencie nadrzędnym nie będzie prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="d7daa-149">Additionally, the scroll range on the parent won't be correct.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="d7daa-150">Zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="d7daa-150">State changes</span></span>

<span data-ttu-id="d7daa-151">Podczas wprowadzania zmian do elementów renderowanych przez `Virtualize` składnik Wywołaj metodę <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wymuszenia ponownej oceny i ponownego renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="d7daa-151">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
