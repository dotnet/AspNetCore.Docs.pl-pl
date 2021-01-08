---
title: BlazorWirtualizacja składników ASP.NET Core
author: guardrex
description: Dowiedz się, jak używać wirtualizacji składników w Blazor aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
ms.openlocfilehash: 706564bb8607d0bb25c092c31a72e5790c825ee4
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024681"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="ae323-103">BlazorWirtualizacja składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae323-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="ae323-104">Autor [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ae323-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ae323-105">Popraw postrzeganą wydajność renderowania składników przy użyciu Blazor wbudowanej obsługi wirtualizacji platformy.</span><span class="sxs-lookup"><span data-stu-id="ae323-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="ae323-106">Wirtualizacja jest techniką do ograniczania renderowania interfejsu użytkownika do zaledwie części, które są obecnie widoczne.</span><span class="sxs-lookup"><span data-stu-id="ae323-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="ae323-107">Na przykład wirtualizacja jest przydatna, gdy aplikacja musi renderować długą listę elementów, a tylko podzbiór elementów ma być widoczny w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="ae323-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="ae323-108">Blazorudostępnia [ `Virtualize` składnik](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) , który może służyć do dodawania wirtualizacji do składników aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ae323-108">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="ae323-109">`Virtualize`Składnika można użyć, gdy:</span><span class="sxs-lookup"><span data-stu-id="ae323-109">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="ae323-110">Renderowanie zestawu elementów danych w pętli.</span><span class="sxs-lookup"><span data-stu-id="ae323-110">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="ae323-111">Większość elementów nie jest widoczna z powodu przewijania.</span><span class="sxs-lookup"><span data-stu-id="ae323-111">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="ae323-112">Renderowane elementy mają dokładnie taki sam rozmiar.</span><span class="sxs-lookup"><span data-stu-id="ae323-112">The rendered items are exactly the same size.</span></span> <span data-ttu-id="ae323-113">Gdy użytkownik przewija do dowolnego punktu, składnik może obliczyć widoczne elementy do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="ae323-113">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="ae323-114">Bez wirtualizacji typowa lista może używać pętli języka C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) do renderowania każdego elementu na liście:</span><span class="sxs-lookup"><span data-stu-id="ae323-114">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="ae323-115">Jeśli lista zawiera tysiące elementów, renderowanie listy może zająć dużo czasu.</span><span class="sxs-lookup"><span data-stu-id="ae323-115">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="ae323-116">Użytkownik może napotkać zauważalne opóźnienie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ae323-116">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="ae323-117">Zamiast wyrenderować każdy element na liście wszystkie jednocześnie, Zastąp [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pętlę `Virtualize` składnikiem i określ stałe źródło elementu z <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ae323-117">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ae323-118">Renderowane są tylko te elementy, które są obecnie widoczne:</span><span class="sxs-lookup"><span data-stu-id="ae323-118">Only the items that are currently visible are rendered:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="ae323-119">Jeśli nie określisz kontekstu do składnika przy użyciu `Context` , użyj `context` wartości ( `context.{PROPERTY}` / `@context.{PROPERTY}` ) w szablonie zawartości elementu:</span><span class="sxs-lookup"><span data-stu-id="ae323-119">If not specifying a context to the component with `Context`, use the `context` value (`context.{PROPERTY}`/`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="ae323-120">Proces mapowania obiektów modelu do elementów i składników można kontrolować za pomocą `@key` atrybutu dyrektywy [] [linki XREF: MVC/views/Razor # Key].</span><span class="sxs-lookup"><span data-stu-id="ae323-120">The mapping process of model objects to elements and components can be controlled with the [`@key`][xref:mvc/views/razor#key] directive attribute.</span></span> <span data-ttu-id="ae323-121">`@key` powoduje, że algorytm różnicowego gwarantuje zachowywanie elementów lub składników na podstawie wartości klucza.</span><span class="sxs-lookup"><span data-stu-id="ae323-121">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="ae323-122">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="ae323-122">For more information, see the following articles:</span></span>
>
> <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> <xref:mvc/views/razor#key>

<span data-ttu-id="ae323-123">`Virtualize`Składnik:</span><span class="sxs-lookup"><span data-stu-id="ae323-123">The `Virtualize` component:</span></span>

* <span data-ttu-id="ae323-124">Oblicza liczbę elementów, które mają być renderowane na podstawie wysokości kontenera i rozmiaru renderowanych elementów.</span><span class="sxs-lookup"><span data-stu-id="ae323-124">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="ae323-125">Ponownie oblicza i renderuje elementy podczas przewijania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ae323-125">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="ae323-126">Pobiera tylko fragmenty rekordów z zewnętrznego interfejsu API, który odpowiada bieżącemu widocznemu regionowi, zamiast pobierać wszystkie dane z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="ae323-126">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="ae323-127">Zawartość elementu `Virtualize` składnika może obejmować:</span><span class="sxs-lookup"><span data-stu-id="ae323-127">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="ae323-128">Zwykły kod HTML i Razor kodu, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="ae323-128">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="ae323-129">Co najmniej jeden Razor składnik.</span><span class="sxs-lookup"><span data-stu-id="ae323-129">One or more Razor components.</span></span>
* <span data-ttu-id="ae323-130">Mieszane składniki HTML/ Razor i Razor .</span><span class="sxs-lookup"><span data-stu-id="ae323-130">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="ae323-131">Delegat dostawcy elementów</span><span class="sxs-lookup"><span data-stu-id="ae323-131">Item provider delegate</span></span>

<span data-ttu-id="ae323-132">Jeśli nie chcesz ładować wszystkich elementów do pamięci, możesz określić metodę delegata dostawcy elementów dla parametru składnika, <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> który asynchronicznie pobiera żądane elementy na żądanie:</span><span class="sxs-lookup"><span data-stu-id="ae323-132">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="ae323-133">Dostawca elementów odbiera obiekt <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , który określa wymaganą liczbę elementów rozpoczynającą się określonym indeksem początkowym.</span><span class="sxs-lookup"><span data-stu-id="ae323-133">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="ae323-134">Następnie dostawca elementów pobiera żądane elementy z bazy danych lub innej usługi i zwraca je <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> wraz z liczbą całkowitej liczby elementów.</span><span class="sxs-lookup"><span data-stu-id="ae323-134">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="ae323-135">Dostawca elementów może wybrać pobieranie elementów z każdym żądaniem lub buforowanie ich w taki sposób, aby były one łatwo dostępne.</span><span class="sxs-lookup"><span data-stu-id="ae323-135">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="ae323-136">`Virtualize`Składnik może akceptować tylko **jedno źródło elementu** z jego parametrów, więc nie próbuj jednocześnie używać dostawcy elementów i przypisywać kolekcji do programu `Items` .</span><span class="sxs-lookup"><span data-stu-id="ae323-136">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="ae323-137">Jeśli oba są przypisane, <xref:System.InvalidOperationException> jest generowany, gdy parametry składnika są ustawione w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="ae323-137">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="ae323-138">Poniższy przykład ładuje pracowników z `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="ae323-138">The following example loads employees from an `EmployeeService`:</span></span>

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

<span data-ttu-id="ae323-139"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instruuje składnik, aby zażądał od niego danych <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> .</span><span class="sxs-lookup"><span data-stu-id="ae323-139"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="ae323-140">Jest to przydatne, gdy zmieniają się dane zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="ae323-140">This is useful when external data changes.</span></span> <span data-ttu-id="ae323-141">Nie ma potrzeby wywoływania tego podczas korzystania z programu <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .</span><span class="sxs-lookup"><span data-stu-id="ae323-141">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="ae323-142">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="ae323-142">Placeholder</span></span>

<span data-ttu-id="ae323-143">Ponieważ żądanie elementów ze zdalnego źródła danych może zająć trochę czasu, istnieje możliwość renderowania symbolu zastępczego z zawartością elementu:</span><span class="sxs-lookup"><span data-stu-id="ae323-143">Because requesting items from a remote data source might take some time, you have the option to render a placeholder  with item content:</span></span>

* <span data-ttu-id="ae323-144">Użyj <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ), aby wyświetlić zawartość do momentu udostępnienia danych elementu.</span><span class="sxs-lookup"><span data-stu-id="ae323-144">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="ae323-145">Użyj <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> , aby ustawić szablon elementu na liście.</span><span class="sxs-lookup"><span data-stu-id="ae323-145">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="ae323-146">Rozmiar elementu</span><span class="sxs-lookup"><span data-stu-id="ae323-146">Item size</span></span>

<span data-ttu-id="ae323-147">Można ustawić rozmiar każdego elementu w pikselach <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (domyślnie: 50px):</span><span class="sxs-lookup"><span data-stu-id="ae323-147">The size of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="ae323-148">Liczba przeskanowania</span><span class="sxs-lookup"><span data-stu-id="ae323-148">Overscan count</span></span>

<span data-ttu-id="ae323-149"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> Określa, ile dodatkowych elementów jest renderowanych przed i po widocznym regionie.</span><span class="sxs-lookup"><span data-stu-id="ae323-149"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="ae323-150">To ustawienie pomaga zmniejszyć częstotliwość renderowania podczas przewijania.</span><span class="sxs-lookup"><span data-stu-id="ae323-150">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="ae323-151">Jednak wyższe wartości powodują więcej elementów renderowanych na stronie (domyślnie: 3):</span><span class="sxs-lookup"><span data-stu-id="ae323-151">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="ae323-152">Zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="ae323-152">State changes</span></span>

<span data-ttu-id="ae323-153">Podczas wprowadzania zmian do elementów renderowanych przez `Virtualize` składnik Wywołaj metodę <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wymuszenia ponownej oceny i ponownego renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="ae323-153">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
