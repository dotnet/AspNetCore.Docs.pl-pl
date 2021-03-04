---
title: BlazorWirtualizacja składników ASP.NET Core
author: guardrex
description: Dowiedz się, jak używać wirtualizacji składników w Blazor aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2021
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
uid: blazor/components/virtualization
ms.openlocfilehash: c81732c29b262e9134a4ff7dab077a4f31db96af
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109822"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="1563e-103">BlazorWirtualizacja składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1563e-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="1563e-104">Popraw postrzeganą wydajność renderowania składników przy użyciu Blazor wbudowanej obsługi wirtualizacji w strukturze ze [ `Virtualize` składnikiem](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601).</span><span class="sxs-lookup"><span data-stu-id="1563e-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support with the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601).</span></span> <span data-ttu-id="1563e-105">Wirtualizacja jest techniką do ograniczania renderowania interfejsu użytkownika do zaledwie części, które są obecnie widoczne.</span><span class="sxs-lookup"><span data-stu-id="1563e-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="1563e-106">Na przykład wirtualizacja jest przydatna, gdy aplikacja musi renderować długą listę elementów, a tylko podzbiór elementów ma być widoczny w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="1563e-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span>

<span data-ttu-id="1563e-107">Użyj `Virtualize` składnika, gdy:</span><span class="sxs-lookup"><span data-stu-id="1563e-107">Use the `Virtualize` component when:</span></span>

* <span data-ttu-id="1563e-108">Renderowanie zestawu elementów danych w pętli.</span><span class="sxs-lookup"><span data-stu-id="1563e-108">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="1563e-109">Większość elementów nie jest widoczna z powodu przewijania.</span><span class="sxs-lookup"><span data-stu-id="1563e-109">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="1563e-110">Renderowane elementy mają ten sam rozmiar.</span><span class="sxs-lookup"><span data-stu-id="1563e-110">The rendered items are the same size.</span></span>

<span data-ttu-id="1563e-111">Gdy użytkownik przewija do dowolnego punktu na `Virtualize` liście elementów składnika, składnik oblicza widoczne elementy do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="1563e-111">When the user scrolls to an arbitrary point in the `Virtualize` component's list of items, the component calculates the visible items to show.</span></span> <span data-ttu-id="1563e-112">Niewidoczne elementy nie są renderowane.</span><span class="sxs-lookup"><span data-stu-id="1563e-112">Unseen items aren't rendered.</span></span>

<span data-ttu-id="1563e-113">Bez wirtualizacji typowa lista może używać pętli języka C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) do renderowania każdego elementu na liście.</span><span class="sxs-lookup"><span data-stu-id="1563e-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in a list.</span></span> <span data-ttu-id="1563e-114">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1563e-114">In the following example:</span></span>

* <span data-ttu-id="1563e-115">`allFlights` jest kolekcją lotów samolotowych.</span><span class="sxs-lookup"><span data-stu-id="1563e-115">`allFlights` is a collection of airplane flights.</span></span>
* <span data-ttu-id="1563e-116">`FlightSummary`Składnik wyświetla szczegółowe informacje o każdym locie.</span><span class="sxs-lookup"><span data-stu-id="1563e-116">The `FlightSummary` component displays details about each flight.</span></span>
* <span data-ttu-id="1563e-117">[ `@key` Atrybut dyrektywy](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components) zachowuje relacje poszczególnych `FlightSummary` składników z przetworzonym lotem przez lot `FlightId` .</span><span class="sxs-lookup"><span data-stu-id="1563e-117">The [`@key` directive attribute](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components) preserves the relationship of each `FlightSummary` component to its rendered flight by the flight's `FlightId`.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="1563e-118">Jeśli kolekcja zawiera tysiące lotów, renderowanie lotów zajmuje dużo czasu, a użytkownicy napotykają zauważalne opóźnienia interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1563e-118">If the collection contains thousands of flights, rendering the flights takes a long time and users experience a noticeable UI lag.</span></span> <span data-ttu-id="1563e-119">Większość lotów nie jest renderowanych, ponieważ wykraczają poza wysokość `<div>` elementu.</span><span class="sxs-lookup"><span data-stu-id="1563e-119">Most of the flights aren't rendered because they fall outside of the height of the `<div>` element.</span></span>

<span data-ttu-id="1563e-120">Zamiast renderowania całą listę lotów jednocześnie Zastąp [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pętlę w powyższym przykładzie ze `Virtualize` składnikiem:</span><span class="sxs-lookup"><span data-stu-id="1563e-120">Instead of rendering the entire list of flights at once, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop in the preceding example with the `Virtualize` component:</span></span>

* <span data-ttu-id="1563e-121">Określ `allFlights` jako źródło elementu stałego <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1563e-121">Specify `allFlights` as a fixed item source to <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1563e-122">Tylko obecnie widoczne loty są renderowane przez `Virtualize` składnik.</span><span class="sxs-lookup"><span data-stu-id="1563e-122">Only the currently visible flights are rendered by the `Virtualize` component.</span></span>
* <span data-ttu-id="1563e-123">Określ kontekst dla każdego lotu z `Context` parametrem.</span><span class="sxs-lookup"><span data-stu-id="1563e-123">Specify a context for each flight with the `Context` parameter.</span></span> <span data-ttu-id="1563e-124">W poniższym przykładzie `flight` jest używany jako kontekst, który zapewnia dostęp do członków każdego lotu.</span><span class="sxs-lookup"><span data-stu-id="1563e-124">In the following example, `flight` is used as the context, which provides access to each flight's members.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="1563e-125">Jeśli kontekst nie jest określony za pomocą `Context` parametru, użyj wartości `context` w szablonie zawartości elementu, aby uzyskać dostęp do wszystkich członków lotu:</span><span class="sxs-lookup"><span data-stu-id="1563e-125">If a context isn't specified with the `Context` parameter, use the value of `context` in the item content template to access each flight's members:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="1563e-126">`Virtualize`Składnik:</span><span class="sxs-lookup"><span data-stu-id="1563e-126">The `Virtualize` component:</span></span>

* <span data-ttu-id="1563e-127">Oblicza liczbę elementów do renderowania na podstawie wysokości kontenera i rozmiaru renderowanych elementów.</span><span class="sxs-lookup"><span data-stu-id="1563e-127">Calculates the number of items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="1563e-128">Ponownie oblicza i renderuje elementy podczas przewijania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1563e-128">Recalculates and rerenders the items as the user scrolls.</span></span>
* <span data-ttu-id="1563e-129">Pobiera tylko fragmenty rekordów z zewnętrznego interfejsu API, który odpowiada bieżącemu widocznemu regionowi, zamiast pobierać wszystkie dane z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="1563e-129">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="1563e-130">Zawartość elementu `Virtualize` składnika może obejmować:</span><span class="sxs-lookup"><span data-stu-id="1563e-130">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="1563e-131">Zwykły kod HTML i Razor kodu, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="1563e-131">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="1563e-132">Co najmniej jeden Razor składnik.</span><span class="sxs-lookup"><span data-stu-id="1563e-132">One or more Razor components.</span></span>
* <span data-ttu-id="1563e-133">Mieszane składniki HTML/ Razor i Razor .</span><span class="sxs-lookup"><span data-stu-id="1563e-133">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="1563e-134">Delegat dostawcy elementów</span><span class="sxs-lookup"><span data-stu-id="1563e-134">Item provider delegate</span></span>

<span data-ttu-id="1563e-135">Jeśli nie chcesz ładować wszystkich elementów do pamięci, możesz określić metodę delegata dostawcy elementów dla parametru składnika, <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> który asynchronicznie pobiera żądane elementy na żądanie.</span><span class="sxs-lookup"><span data-stu-id="1563e-135">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="1563e-136">W poniższym przykładzie `LoadEmployees` Metoda dostarcza elementy do `Virtualize` składnika:</span><span class="sxs-lookup"><span data-stu-id="1563e-136">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="1563e-137">Dostawca elementów odbiera obiekt <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , który określa wymaganą liczbę elementów rozpoczynającą się określonym indeksem początkowym.</span><span class="sxs-lookup"><span data-stu-id="1563e-137">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="1563e-138">Następnie dostawca elementów pobiera żądane elementy z bazy danych lub innej usługi i zwraca je <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> wraz z liczbą całkowitej liczby elementów.</span><span class="sxs-lookup"><span data-stu-id="1563e-138">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="1563e-139">Dostawca elementów może wybrać pobieranie elementów z każdym żądaniem lub buforowanie ich w taki sposób, aby były one łatwo dostępne.</span><span class="sxs-lookup"><span data-stu-id="1563e-139">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="1563e-140">`Virtualize`Składnik może akceptować tylko **jedno źródło elementu** z jego parametrów, więc nie próbuj jednocześnie używać dostawcy elementów i przypisywać kolekcji do programu `Items` .</span><span class="sxs-lookup"><span data-stu-id="1563e-140">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="1563e-141">Jeśli oba są przypisane, <xref:System.InvalidOperationException> jest generowany, gdy parametry składnika są ustawione w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="1563e-141">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="1563e-142">Poniższy `LoadEmployees` przykład metody ładuje pracowników z `EmployeeService` (niepokazywany):</span><span class="sxs-lookup"><span data-stu-id="1563e-142">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

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

<span data-ttu-id="1563e-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instruuje składnik, aby zażądał od niego danych <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> .</span><span class="sxs-lookup"><span data-stu-id="1563e-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="1563e-144">Jest to przydatne, gdy zmieniają się dane zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="1563e-144">This is useful when external data changes.</span></span> <span data-ttu-id="1563e-145">Nie ma potrzeby wywoływania <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> przy użyciu <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .</span><span class="sxs-lookup"><span data-stu-id="1563e-145">There's no need to call <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="1563e-146">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="1563e-146">Placeholder</span></span>

<span data-ttu-id="1563e-147">Ponieważ żądanie elementów ze zdalnego źródła danych może zająć trochę czasu, istnieje możliwość renderowania symbolu zastępczego z zawartością elementu:</span><span class="sxs-lookup"><span data-stu-id="1563e-147">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="1563e-148">Użyj <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ), aby wyświetlić zawartość do momentu udostępnienia danych elementu.</span><span class="sxs-lookup"><span data-stu-id="1563e-148">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="1563e-149">Użyj <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> , aby ustawić szablon elementu na liście.</span><span class="sxs-lookup"><span data-stu-id="1563e-149">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="1563e-150">Rozmiar elementu</span><span class="sxs-lookup"><span data-stu-id="1563e-150">Item size</span></span>

<span data-ttu-id="1563e-151">Wysokość każdego elementu w pikselach można ustawić za pomocą <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (domyślnie: 50).</span><span class="sxs-lookup"><span data-stu-id="1563e-151">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50).</span></span> <span data-ttu-id="1563e-152">Poniższy przykład zmienia wysokość każdego elementu z domyślnego 50 pikseli na 25 pikseli:</span><span class="sxs-lookup"><span data-stu-id="1563e-152">The following example changes the height of each item from the default of 50 pixels to 25 pixels:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="1563e-153">Domyślnie `Virtualize` składnik mierzy rozmiar renderowania (wysokość) poszczególnych elementów *po* wystąpieniu początkowej renderowania.</span><span class="sxs-lookup"><span data-stu-id="1563e-153">By default, the `Virtualize` component measures the rendering size (height) of individual items *after* the initial render occurs.</span></span> <span data-ttu-id="1563e-154">Użyj <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> , aby zapewnić dokładny rozmiar elementu z wyprzedzeniem, aby pomóc w dokładnym początkowej wydajności renderowania i zapewnić poprawną pozycję przewijania na potrzeby ponownych prób ładowania strony.</span><span class="sxs-lookup"><span data-stu-id="1563e-154">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="1563e-155">Jeśli wartość domyślna <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> powoduje, że niektóre elementy mają być renderowane poza aktualnie widocznym widokiem, zostanie wyzwolone drugie ponowne renderowanie.</span><span class="sxs-lookup"><span data-stu-id="1563e-155">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="1563e-156">Aby prawidłowo zachować położenie przewijania przeglądarki na liście zwirtualizowanej, początkowy rendering musi być prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="1563e-156">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="1563e-157">W przeciwnym razie użytkownicy mogą wyświetlić błędne elementy.</span><span class="sxs-lookup"><span data-stu-id="1563e-157">If not, users might view the wrong items.</span></span>

## <a name="overscan-count"></a><span data-ttu-id="1563e-158">Liczba przeskanowania</span><span class="sxs-lookup"><span data-stu-id="1563e-158">Overscan count</span></span>

<span data-ttu-id="1563e-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> Określa, ile dodatkowych elementów jest renderowanych przed i po widocznym regionie.</span><span class="sxs-lookup"><span data-stu-id="1563e-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="1563e-160">To ustawienie pomaga zmniejszyć częstotliwość renderowania podczas przewijania.</span><span class="sxs-lookup"><span data-stu-id="1563e-160">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="1563e-161">Jednak wyższe wartości powodują więcej elementów renderowanych na stronie (domyślnie: 3).</span><span class="sxs-lookup"><span data-stu-id="1563e-161">However, higher values result in more elements rendered in the page (default: 3).</span></span> <span data-ttu-id="1563e-162">Poniższy przykład zmienia liczbę przeskanów z domyślnego z trzech elementów na cztery elementy:</span><span class="sxs-lookup"><span data-stu-id="1563e-162">The following example changes the overscan count from the default of three items to four items:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="1563e-163">Zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="1563e-163">State changes</span></span>

<span data-ttu-id="1563e-164">Podczas wprowadzania zmian do elementów renderowanych przez `Virtualize` składnik Wywołaj metodę <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wymuszenia ponownej oceny i ponownego renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="1563e-164">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="1563e-165">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="1563e-165">For more information, see <xref:blazor/components/rendering>.</span></span>
