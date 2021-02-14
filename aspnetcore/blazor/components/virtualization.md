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
ms.openlocfilehash: d9fc767a4b5160c616053b075ba92194bcffa275
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280023"
---
# <a name="aspnet-core-blazor-component-virtualization"></a>BlazorWirtualizacja składników ASP.NET Core

Popraw postrzeganą wydajność renderowania składników przy użyciu Blazor wbudowanej obsługi wirtualizacji platformy. Wirtualizacja jest techniką do ograniczania renderowania interfejsu użytkownika do zaledwie części, które są obecnie widoczne. Na przykład wirtualizacja jest przydatna, gdy aplikacja musi renderować długą listę elementów, a tylko podzbiór elementów ma być widoczny w danym momencie. Blazorudostępnia [ `Virtualize` składnik](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) , który może służyć do dodawania wirtualizacji do składników aplikacji.

`Virtualize`Składnika można użyć, gdy:

* Renderowanie zestawu elementów danych w pętli.
* Większość elementów nie jest widoczna z powodu przewijania.
* Renderowane elementy mają dokładnie taki sam rozmiar. Gdy użytkownik przewija do dowolnego punktu, składnik może obliczyć widoczne elementy do wyświetlenia.

Bez wirtualizacji typowa lista może używać pętli języka C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) do renderowania każdego elementu na liście:

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

Jeśli lista zawiera tysiące elementów, renderowanie listy może zająć dużo czasu. Użytkownik może napotkać zauważalne opóźnienie interfejsu użytkownika.

Zamiast wyrenderować każdy element na liście wszystkie jednocześnie, Zastąp [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pętlę `Virtualize` składnikiem i określ stałe źródło elementu z <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> . Renderowane są tylko te elementy, które są obecnie widoczne:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

Jeśli nie określisz kontekstu do składnika przy użyciu `Context` , użyj `context` wartości z szablonu zawartości elementu:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> Proces mapowania obiektów modelu do elementów i składników można kontrolować przy użyciu [`@key`](xref:mvc/views/razor#key) atrybutu dyrektywy. `@key` powoduje, że algorytm różnicowego gwarantuje zachowywanie elementów lub składników na podstawie wartości klucza.
>
> Aby uzyskać więcej informacji, zobacz następujące artykuły:
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [Razor odwołanie do składni dla ASP.NET Core](xref:mvc/views/razor#key)

`Virtualize`Składnik:

* Oblicza liczbę elementów, które mają być renderowane na podstawie wysokości kontenera i rozmiaru renderowanych elementów.
* Ponownie oblicza i renderuje elementy podczas przewijania użytkownika.
* Pobiera tylko fragmenty rekordów z zewnętrznego interfejsu API, który odpowiada bieżącemu widocznemu regionowi, zamiast pobierać wszystkie dane z kolekcji.

Zawartość elementu `Virtualize` składnika może obejmować:

* Zwykły kod HTML i Razor kodu, jak pokazano w powyższym przykładzie.
* Co najmniej jeden Razor składnik.
* Mieszane składniki HTML/ Razor i Razor .

## <a name="item-provider-delegate"></a>Delegat dostawcy elementów

Jeśli nie chcesz ładować wszystkich elementów do pamięci, możesz określić metodę delegata dostawcy elementów dla parametru składnika, <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> który asynchronicznie pobiera żądane elementy na żądanie. W poniższym przykładzie `LoadEmployees` Metoda dostarcza elementy do `Virtualize` składnika:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Dostawca elementów odbiera obiekt <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , który określa wymaganą liczbę elementów rozpoczynającą się określonym indeksem początkowym. Następnie dostawca elementów pobiera żądane elementy z bazy danych lub innej usługi i zwraca je <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> wraz z liczbą całkowitej liczby elementów. Dostawca elementów może wybrać pobieranie elementów z każdym żądaniem lub buforowanie ich w taki sposób, aby były one łatwo dostępne.

`Virtualize`Składnik może akceptować tylko **jedno źródło elementu** z jego parametrów, więc nie próbuj jednocześnie używać dostawcy elementów i przypisywać kolekcji do programu `Items` . Jeśli oba są przypisane, <xref:System.InvalidOperationException> jest generowany, gdy parametry składnika są ustawione w czasie wykonywania.

Poniższy `LoadEmployees` przykład metody ładuje pracowników z `EmployeeService` (niepokazywany):

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

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instruuje składnik, aby zażądał od niego danych <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> . Jest to przydatne, gdy zmieniają się dane zewnętrzne. Nie ma potrzeby wywoływania tego podczas korzystania z programu <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .

## <a name="placeholder"></a>Symbol zastępczy

Ponieważ żądanie elementów ze zdalnego źródła danych może zająć trochę czasu, istnieje możliwość renderowania symbolu zastępczego z zawartością elementu:

* Użyj <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ), aby wyświetlić zawartość do momentu udostępnienia danych elementu.
* Użyj <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> , aby ustawić szablon elementu na liście.

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

Wysokość każdego elementu w pikselach można ustawić za pomocą <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (domyślnie: 50):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

Domyślnie `Virtualize` składnik mierzy rzeczywistą wielkość renderowania *po wykonaniu* początkowej renderowania. Użyj <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> , aby zapewnić dokładny rozmiar elementu z wyprzedzeniem, aby pomóc w dokładnym początkowej wydajności renderowania i zapewnić poprawną pozycję przewijania na potrzeby ponownych prób ładowania strony. Jeśli wartość domyślna <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> powoduje, że niektóre elementy mają być renderowane poza aktualnie widocznym widokiem, zostanie wyzwolone drugie ponowne renderowanie. Aby prawidłowo zachować położenie przewijania przeglądarki na liście zwirtualizowanej, początkowy rendering musi być prawidłowy. W przeciwnym razie użytkownicy mogą wyświetlić błędne elementy. 

## <a name="overscan-count"></a>Liczba przeskanowania

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> Określa, ile dodatkowych elementów jest renderowanych przed i po widocznym regionie. To ustawienie pomaga zmniejszyć częstotliwość renderowania podczas przewijania. Jednak wyższe wartości powodują więcej elementów renderowanych na stronie (domyślnie: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Zmiany stanu

Podczas wprowadzania zmian do elementów renderowanych przez `Virtualize` składnik Wywołaj metodę <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wymuszenia ponownej oceny i ponownego renderowania składnika. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.
