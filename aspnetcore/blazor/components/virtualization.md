---
title: BlazorWirtualizacja składników ASP.NET Core
author: guardrex
description: Dowiedz się, jak używać wirtualizacji składników w Blazor aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2020
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
ms.openlocfilehash: 9c3e53bee7535b36bba3474ff50a881568bbd690
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393811"
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

Na przykład siatka lub lista, która renderuje setki wierszy zawierających składniki, ma intensywną obsługę procesora. Rozważ wirtualizację układu siatki lub listy tak, że tylko podzestaw składników jest renderowany w danym momencie. Aby zapoznać się z przykładem renderowania podzestawu składników, zobacz następujące składniki w [ `Virtualization` aplikacji przykładowej (repozytorium w witrynie GitHub/przykłady)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize` składnik ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): składnik zapisany w języku C#, który implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> zestaw wierszy danych pogody na podstawie przewijania przez użytkownika.
* `FetchData` składnik ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): używa `Virtualize` składnika do wyświetlania 25 wierszy danych pogody jednocześnie.

::: moniker-end

## <a name="state-changes"></a>Zmiany stanu

Podczas wprowadzania zmian do elementów renderowanych przez `Virtualize` składnik Wywołaj metodę <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wymuszenia ponownej oceny i ponownego renderowania składnika.
