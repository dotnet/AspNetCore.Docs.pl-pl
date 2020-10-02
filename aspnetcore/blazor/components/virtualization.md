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
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>BlazorWirtualizacja składników ASP.NET Core

Autor [Daniel Roth](https://github.com/danroth27)

Popraw postrzeganą wydajność renderowania składników przy użyciu Blazor wbudowanej obsługi wirtualizacji platformy. Wirtualizacja jest techniką do ograniczania renderowania interfejsu użytkownika do zaledwie części, które są obecnie widoczne. Na przykład wirtualizacja jest przydatna, gdy aplikacja musi renderować długą listę elementów, a tylko podzbiór elementów ma być widoczny w danym momencie. Blazor udostępnia `Virtualize` składnik, który może służyć do dodawania wirtualizacji do składników aplikacji.

::: moniker range=">= aspnetcore-5.0"

Bez wirtualizacji typowa lista może używać pętli języka C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) do renderowania każdego elementu na liście:

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

Jeśli lista zawiera tysiące elementów, renderowanie listy może zająć dużo czasu. Użytkownik może napotkać zauważalne opóźnienie interfejsu użytkownika.

Zamiast wyrenderować każdy element na liście wszystkie jednocześnie, Zastąp [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pętlę `Virtualize` składnikiem i określ stałe źródło elementu z `Items` . Renderowane są tylko te elementy, które są obecnie widoczne:

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Jeśli nie określisz kontekstu do składnika przy użyciu `Context` , użyj `context` wartości ( `@context.{PROPERTY}` ) w szablonie zawartości elementu:

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

`Virtualize`Składnik oblicza, ile elementów ma być renderowanych na podstawie wysokości kontenera i rozmiaru elementów renderowane.

Zawartość elementu `Virtualize` składnika może obejmować:

* Zwykły kod HTML i Razor kodu, jak pokazano w powyższym przykładzie.
* Co najmniej jeden Razor składnik.
* Mieszane składniki HTML/ Razor i Razor .

## <a name="item-provider-delegate"></a>Delegat dostawcy elementów

Jeśli nie chcesz ładować wszystkich elementów do pamięci, możesz określić metodę delegata dostawcy elementów dla parametru składnika, `ItemsProvider` który asynchronicznie pobiera żądane elementy na żądanie:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Dostawca elementów odbiera obiekt `ItemsProviderRequest` , który określa wymaganą liczbę elementów rozpoczynającą się określonym indeksem początkowym. Następnie dostawca elementów pobiera żądane elementy z bazy danych lub innej usługi i zwraca je `ItemsProviderResult<TItem>` wraz z liczbą całkowitej liczby elementów. Dostawca elementów może wybrać pobieranie elementów z każdym żądaniem lub buforowanie ich w taki sposób, aby były one łatwo dostępne. Nie próbuj używać dostawcy elementów i przypisz kolekcję do tego `Items` samego `Virtualize` składnika.

Poniższy przykład ładuje pracowników z `EmployeeService` :

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

## <a name="placeholder"></a>Symbol zastępczy

Ponieważ żądanie elementów ze zdalnego źródła danych może zająć trochę czasu, możesz renderować symbol zastępczy ( `<Placeholder>...</Placeholder>` ), dopóki dane elementu nie będą dostępne:

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

## <a name="item-size"></a>Rozmiar elementu

Można ustawić rozmiar każdego elementu w pikselach `ItemSize` (domyślnie: 50px):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Liczba przeskanowania

`OverscanCount` Określa, ile dodatkowych elementów jest renderowanych przed i po widocznym regionie. To ustawienie pomaga zmniejszyć częstotliwość renderowania podczas przewijania. Jednak wyższe wartości powodują więcej elementów renderowanych na stronie (domyślnie: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Na przykład siatka lub lista, która renderuje setki wierszy zawierających składniki, ma intensywną obsługę procesora. Rozważ wirtualizację układu siatki lub listy tak, że tylko podzestaw składników jest renderowany w danym momencie.

Poniższy `Virtualize` składnik ( `Virtualize.cs` ) implementuje, <xref:Microsoft.AspNetCore.Components.ComponentBase> Aby renderować zawartość podrzędną na podstawie przewijania przez użytkownika:

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

Poniższy `FetchData` składnik ( `FetchData.razor` ) używa poprzedniego `Virtualize` składnika do wyświetlania 25 wierszy danych pogody jednocześnie:

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

W poprzednim przykładzie podejście polega na uniknięciu bezwzględnego pozycjonowania poszczególnych elementów. Pozycjonowanie bezwzględne może mieć pewne zalety (można upewnić się, że elementy zajmują określoną ilość miejsca na osi Y), ale w przypadku zmiany rozmiaru zawartości nie można pobrać kolumn tabeli w górę w wierszach/nagłówkach.

Pojęcie związane z projektem `Virtualize` składnika polega na tym, że składnik nie zmienia sposobu, w jaki elementy są określone w modelu DOM. Nie dodano żadnych elementów otoki, oprócz jednego z nich, którego `TagName` określisz.

Najlepszym rozwiązaniem jest uniknięcie nawet `TagName` elementu otoki. `Virtualize`Składnik nie emituje żadnych elementów. Cały składnik jest renderowany, jednak wiele wystąpień szablonów jest wymaganych do wypełnienia dowolnego pozostałego widocznego miejsca w najbliższym elemencie nadrzędnym z możliwością przewijania, a następnie Dodaj następujący element, który umożliwia przewijanie elementu nadrzędnego. W odniesieniu do danego układu jest to tak samo jak w przypadku, gdy pełen zakres elementów podrzędnych był fizycznie w modelu DOM. Wymaga podania dokładnej precyzji `ItemHeight` . Jeśli okaże się to niewłaściwe (na przykład dlatego, że masz wątpliwości i uważasz, że jest on prawidłowy, aby określić `style.height` w przypadku `<tr>` ), składnik zostanie uruchomiony w celu renderowania nieprawidłowej liczby wystąpień szablonu i nie wypełnia interfejsu użytkownika ani niewydajnego renderowania za dużo. Ponadto zakres przewijania w elemencie nadrzędnym nie będzie prawidłowy.

::: moniker-end

## <a name="state-changes"></a>Zmiany stanu

Podczas wprowadzania zmian do elementów renderowanych przez `Virtualize` składnik Wywołaj metodę <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wymuszenia ponownej oceny i ponownego renderowania składnika.
