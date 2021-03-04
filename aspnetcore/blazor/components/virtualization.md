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
# <a name="aspnet-core-blazor-component-virtualization"></a>BlazorWirtualizacja składników ASP.NET Core

Popraw postrzeganą wydajność renderowania składników przy użyciu Blazor wbudowanej obsługi wirtualizacji w strukturze ze [ `Virtualize` składnikiem](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601). Wirtualizacja jest techniką do ograniczania renderowania interfejsu użytkownika do zaledwie części, które są obecnie widoczne. Na przykład wirtualizacja jest przydatna, gdy aplikacja musi renderować długą listę elementów, a tylko podzbiór elementów ma być widoczny w danym momencie.

Użyj `Virtualize` składnika, gdy:

* Renderowanie zestawu elementów danych w pętli.
* Większość elementów nie jest widoczna z powodu przewijania.
* Renderowane elementy mają ten sam rozmiar.

Gdy użytkownik przewija do dowolnego punktu na `Virtualize` liście elementów składnika, składnik oblicza widoczne elementy do wyświetlenia. Niewidoczne elementy nie są renderowane.

Bez wirtualizacji typowa lista może używać pętli języka C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) do renderowania każdego elementu na liście. W poniższym przykładzie:

* `allFlights` jest kolekcją lotów samolotowych.
* `FlightSummary`Składnik wyświetla szczegółowe informacje o każdym locie.
* [ `@key` Atrybut dyrektywy](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components) zachowuje relacje poszczególnych `FlightSummary` składników z przetworzonym lotem przez lot `FlightId` .

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

Jeśli kolekcja zawiera tysiące lotów, renderowanie lotów zajmuje dużo czasu, a użytkownicy napotykają zauważalne opóźnienia interfejsu użytkownika. Większość lotów nie jest renderowanych, ponieważ wykraczają poza wysokość `<div>` elementu.

Zamiast renderowania całą listę lotów jednocześnie Zastąp [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pętlę w powyższym przykładzie ze `Virtualize` składnikiem:

* Określ `allFlights` jako źródło elementu stałego <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> . Tylko obecnie widoczne loty są renderowane przez `Virtualize` składnik.
* Określ kontekst dla każdego lotu z `Context` parametrem. W poniższym przykładzie `flight` jest używany jako kontekst, który zapewnia dostęp do członków każdego lotu.

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

Jeśli kontekst nie jest określony za pomocą `Context` parametru, użyj wartości `context` w szablonie zawartości elementu, aby uzyskać dostęp do wszystkich członków lotu:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

`Virtualize`Składnik:

* Oblicza liczbę elementów do renderowania na podstawie wysokości kontenera i rozmiaru renderowanych elementów.
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

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instruuje składnik, aby zażądał od niego danych <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> . Jest to przydatne, gdy zmieniają się dane zewnętrzne. Nie ma potrzeby wywoływania <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> przy użyciu <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .

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

Wysokość każdego elementu w pikselach można ustawić za pomocą <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (domyślnie: 50). Poniższy przykład zmienia wysokość każdego elementu z domyślnego 50 pikseli na 25 pikseli:

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

Domyślnie `Virtualize` składnik mierzy rozmiar renderowania (wysokość) poszczególnych elementów *po* wystąpieniu początkowej renderowania. Użyj <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> , aby zapewnić dokładny rozmiar elementu z wyprzedzeniem, aby pomóc w dokładnym początkowej wydajności renderowania i zapewnić poprawną pozycję przewijania na potrzeby ponownych prób ładowania strony. Jeśli wartość domyślna <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> powoduje, że niektóre elementy mają być renderowane poza aktualnie widocznym widokiem, zostanie wyzwolone drugie ponowne renderowanie. Aby prawidłowo zachować położenie przewijania przeglądarki na liście zwirtualizowanej, początkowy rendering musi być prawidłowy. W przeciwnym razie użytkownicy mogą wyświetlić błędne elementy.

## <a name="overscan-count"></a>Liczba przeskanowania

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> Określa, ile dodatkowych elementów jest renderowanych przed i po widocznym regionie. To ustawienie pomaga zmniejszyć częstotliwość renderowania podczas przewijania. Jednak wyższe wartości powodują więcej elementów renderowanych na stronie (domyślnie: 3). Poniższy przykład zmienia liczbę przeskanów z domyślnego z trzech elementów na cztery elementy:

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Zmiany stanu

Podczas wprowadzania zmian do elementów renderowanych przez `Virtualize` składnik Wywołaj metodę <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wymuszenia ponownej oceny i ponownego renderowania składnika. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.
