---
title: BlazorWirtualizacja składników ASP.NET Core
author: guardrex
description: Dowiedz się, jak używać wirtualizacji składników w Blazor aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/16/2020
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
ms.openlocfilehash: c904a3f330531f80d1dc69bf63621c59790e1cb0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722999"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="0ca77-103">BlazorWirtualizacja składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ca77-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="0ca77-104">Autor [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="0ca77-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="0ca77-105">Popraw postrzeganą wydajność renderowania składników przy użyciu Blazor wbudowanej obsługi wirtualizacji platformy.</span><span class="sxs-lookup"><span data-stu-id="0ca77-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="0ca77-106">Wirtualizacja jest techniką do ograniczania renderowania interfejsu użytkownika do zaledwie części, które są obecnie widoczne.</span><span class="sxs-lookup"><span data-stu-id="0ca77-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="0ca77-107">Na przykład wirtualizacja jest przydatna, gdy aplikacja musi renderować długą listę lub tabelę z wieloma wierszami, a tylko podzbiór elementów będzie widoczny w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="0ca77-107">For example, virtualization is helpful when the app must render a long list or a table with many rows and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="0ca77-108">Blazor udostępnia `Virtualize` składnik, który może służyć do dodawania wirtualizacji do składników aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ca77-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0ca77-109">Bez wirtualizacji, typowa lista lub składnik oparty na tabeli może używać pętli języka C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) do renderowania każdego elementu na liście lub każdego wiersza w tabeli:</span><span class="sxs-lookup"><span data-stu-id="0ca77-109">Without virtualization, a typical list or table-based component might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list or each row in the table:</span></span>

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

<span data-ttu-id="0ca77-110">Jeśli lista zawiera tysiące elementów, renderowanie listy może zająć dużo czasu.</span><span class="sxs-lookup"><span data-stu-id="0ca77-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="0ca77-111">Użytkownik może napotkać zauważalne opóźnienie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0ca77-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="0ca77-112">Zamiast wyrenderować każdy element na liście wszystkie jednocześnie, Zastąp [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pętlę `Virtualize` składnikiem i określ stałe źródło elementu z `Items` .</span><span class="sxs-lookup"><span data-stu-id="0ca77-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="0ca77-113">Renderowane są tylko te elementy, które są obecnie widoczne:</span><span class="sxs-lookup"><span data-stu-id="0ca77-113">Only the items that are currently visible are rendered:</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="0ca77-114">Jeśli nie określisz kontekstu do składnika przy użyciu `Context` , użyj `context` wartości ( `@context.{PROPERTY}` ) w szablonie zawartości elementu:</span><span class="sxs-lookup"><span data-stu-id="0ca77-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="0ca77-115">`Virtualize`Składnik oblicza, ile elementów ma być renderowanych na podstawie wysokości kontenera i rozmiaru elementów renderowane.</span><span class="sxs-lookup"><span data-stu-id="0ca77-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="0ca77-116">Delegat dostawcy elementów</span><span class="sxs-lookup"><span data-stu-id="0ca77-116">Item provider delegate</span></span>

<span data-ttu-id="0ca77-117">Jeśli nie chcesz ładować wszystkich elementów do pamięci, możesz określić metodę delegata dostawcy elementów dla parametru składnika, `ItemsProvider` który asynchronicznie pobiera żądane elementy na żądanie:</span><span class="sxs-lookup"><span data-stu-id="0ca77-117">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="0ca77-118">Dostawca elementów odbiera obiekt `ItemsProviderRequest` , który określa wymaganą liczbę elementów rozpoczynającą się określonym indeksem początkowym.</span><span class="sxs-lookup"><span data-stu-id="0ca77-118">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="0ca77-119">Następnie dostawca elementów pobiera żądane elementy z bazy danych lub innej usługi i zwraca je `ItemsProviderResult<TItem>` wraz z liczbą całkowitej liczby elementów.</span><span class="sxs-lookup"><span data-stu-id="0ca77-119">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="0ca77-120">Dostawca elementów może wybrać pobieranie elementów z każdym żądaniem lub buforowanie ich w taki sposób, aby były one łatwo dostępne.</span><span class="sxs-lookup"><span data-stu-id="0ca77-120">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="0ca77-121">Nie próbuj używać dostawcy elementów i przypisz kolekcję do tego `Items` samego `Virtualize` składnika.</span><span class="sxs-lookup"><span data-stu-id="0ca77-121">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="0ca77-122">Poniższy przykład ładuje pracowników z `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="0ca77-122">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="0ca77-123">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="0ca77-123">Placeholder</span></span>

<span data-ttu-id="0ca77-124">Ponieważ żądanie elementów ze zdalnego źródła danych może zająć trochę czasu, możesz renderować symbol zastępczy ( `<Placeholder>...</Placeholder>` ), dopóki dane elementu nie będą dostępne:</span><span class="sxs-lookup"><span data-stu-id="0ca77-124">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a><span data-ttu-id="0ca77-125">Rozmiar elementu</span><span class="sxs-lookup"><span data-stu-id="0ca77-125">Item size</span></span>

<span data-ttu-id="0ca77-126">Można ustawić rozmiar każdego elementu w pikselach `ItemSize` (domyślnie: 50px):</span><span class="sxs-lookup"><span data-stu-id="0ca77-126">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a><span data-ttu-id="0ca77-127">Liczba przeskanowania</span><span class="sxs-lookup"><span data-stu-id="0ca77-127">Overscan count</span></span>

<span data-ttu-id="0ca77-128">`OverscanCount` Określa, ile dodatkowych elementów jest renderowanych przed i po widocznym regionie.</span><span class="sxs-lookup"><span data-stu-id="0ca77-128">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="0ca77-129">To ustawienie pomaga zmniejszyć częstotliwość renderowania podczas przewijania.</span><span class="sxs-lookup"><span data-stu-id="0ca77-129">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="0ca77-130">Jednak wyższe wartości powodują więcej elementów renderowanych na stronie (domyślnie: 3):</span><span class="sxs-lookup"><span data-stu-id="0ca77-130">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="0ca77-131">Na przykład siatka lub lista, która renderuje setki wierszy zawierających składniki, ma intensywną obsługę procesora.</span><span class="sxs-lookup"><span data-stu-id="0ca77-131">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="0ca77-132">Rozważ wirtualizację układu siatki lub listy tak, że tylko podzestaw składników jest renderowany w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="0ca77-132">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="0ca77-133">Aby zapoznać się z przykładem renderowania podzestawu składników, zobacz następujące składniki w [ `Virtualization` aplikacji przykładowej (repozytorium w witrynie GitHub/przykłady)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="0ca77-133">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="0ca77-134">`Virtualize` składnik ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): składnik zapisany w języku C#, który implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> zestaw wierszy danych pogody na podstawie przewijania przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0ca77-134">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="0ca77-135">`FetchData` składnik ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): używa `Virtualize` składnika do wyświetlania 25 wierszy danych pogody jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="0ca77-135">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end
