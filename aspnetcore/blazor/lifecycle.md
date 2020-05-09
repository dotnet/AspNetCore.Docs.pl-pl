---
title: ASP.NET Core Blazor cykl życia
author: guardrex
description: Dowiedz się, Razor jak korzystać z metod cyklu Blazor życia składników w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 81699158a161d0e9c9621235840979ebcd634a7e
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976704"
---
# <a name="aspnet-core-blazor-lifecycle"></a>ASP.NET Core Blazor cykl życia

Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)

Blazor Platforma obejmuje metody cyklu życia synchronicznego i asynchronicznego. Zastąp metody cyklu życia, aby wykonać dodatkowe operacje na składnikach podczas inicjowania i renderowania składnika.

## <a name="lifecycle-methods"></a>Metody cyklu życia

### <a name="component-initialization-methods"></a>Metody inicjujące składniki

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> są wywoływane, gdy składnik zostanie zainicjowany po odebraniu początkowych parametrów z jego składnika nadrzędnego. Użyj `OnInitializedAsync` , gdy składnik wykonuje operację asynchroniczną i powinien być odświeżany po zakończeniu operacji.

W przypadku operacji synchronicznej Przesłoń `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Aby wykonać operację asynchroniczną, przesłonić `OnInitializedAsync` i `await` użyć słowa kluczowego w operacji:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorAplikacje serwera, które wywołują `OnInitializedAsync` **_dwa razy_** wywołanie [zawartości](xref:blazor/hosting-model-configuration#render-mode) :

* Gdy składnik jest początkowo renderowany statycznie jako część strony.
* Drugi raz, gdy przeglądarka nawiąże połączenie z serwerem.

Aby zapobiec dwukrotnemu `OnInitializedAsync` uruchomieniu kodu dewelopera, zapoznaj się z sekcją [ponowne łączenie po prerenderowania](#stateful-reconnection-after-prerendering) .

Gdy aplikacja Blazor serwera jest wstępnie renderowana, niektóre akcje, takie jak wywoływanie kodu JavaScript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało nawiązane. Składniki mogą być konieczne w różny sposób, gdy są wstępnie renderowane. Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy aplikacja jest prerenderowana](#detect-when-the-app-is-prerendering) .

W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia. Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika z](#component-disposal-with-idisposable) interfejsem IDisposable.

### <a name="before-parameters-are-set"></a>Przed ustawieniem parametrów

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>Ustawia parametry dostarczone przez element nadrzędny składnika w drzewie renderowania:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>zawiera cały zestaw wartości parametrów każdorazowo `SetParametersAsync` wywoływana.

Domyślna implementacja `SetParametersAsync` programu ustawia wartość każdej właściwości z atrybutem `[Parameter]` lub `[CascadingParameter]` , który ma odpowiednią wartość w. `ParameterView` Parametry, które nie mają odpowiedniej wartości w `ParameterView` , pozostają bez zmian.

Jeśli `base.SetParametersAync` nie zostanie wywołana, kod niestandardowy może interpretować wartość parametrów przychodzących w dowolny sposób. Na przykład nie jest wymagane przypisanie parametrów przychodzących do właściwości w klasie.

W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia. Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika z](#component-disposal-with-idisposable) interfejsem IDisposable.

### <a name="after-parameters-are-set"></a>Po ustawieniu parametrów

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> są wywoływane:

* Gdy składnik jest zainicjowany i odebrał swój pierwszy zestaw parametrów z jego składnika nadrzędnego.
* Po ponownym wyrenderowaniu i zaopatrzeniu składnika nadrzędnego:
  * Tylko znane niezmienne typy pierwotne, których co najmniej jeden parametr został zmieniony.
  * Wszystkie parametry złożone z typem. Struktura nie może wiedzieć, czy wartości parametru złożonego są mutacją wewnętrznie, dlatego traktuje zestaw parametrów jako zmieniony.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Asynchroniczna działanie, gdy stosowane są `OnParametersSetAsync` parametry i wartości właściwości, muszą wystąpić podczas zdarzenia cyklu życia.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia. Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika z](#component-disposal-with-idisposable) interfejsem IDisposable.

### <a name="after-component-render"></a>Po renderowania składników

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> są wywoływane po zakończeniu renderowania składnika. Odwołania do elementów i składników są wypełniane w tym momencie. Ten etap służy do wykonywania dodatkowych kroków inicjowania przy użyciu renderowanej zawartości, takiej jak aktywacja bibliotek języka JavaScript innych firm, które działają na renderowanych elementach DOM.

`firstRender` Parametr dla `OnAfterRenderAsync` i `OnAfterRender`:

* Jest ustawiany `true` po raz pierwszy, gdy wystąpienie składnika jest renderowane.
* Można go użyć, aby upewnić się, że zadania inicjowania są wykonywane tylko raz.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> Asynchroniczne działanie natychmiast po wyrenderowaniu musi wystąpić `OnAfterRenderAsync` w trakcie zdarzenia cyklu życia.
>
> Nawet w przypadku powrotu <xref:System.Threading.Tasks.Task> z `OnAfterRenderAsync`programu, struktura nie planuje dalszych cykli renderowania dla składnika po zakończeniu tego zadania. Ma to na celu uniknięcie nieskończonej pętli renderowania. Różnią się one od innych metod cyklu życia, które Zaplanuj kolejny cykl renderowania po zakończeniu zwracanego zadania.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender`i `OnAfterRenderAsync` *nie są wywoływane podczas renderowania na serwerze.*

W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia. Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika z](#component-disposal-with-idisposable) interfejsem IDisposable.

### <a name="suppress-ui-refreshing"></a>Pomiń odświeżanie interfejsu użytkownika

Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , aby pominąć odświeżanie interfejsu użytkownika. Jeśli implementacja zwraca `true`, interfejs użytkownika zostanie odświeżony:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`jest wywoływana za każdym razem, gdy składnik jest renderowany.

Nawet jeśli `ShouldRender` jest zastępowany, składnik jest zawsze początkowo renderowany.

## <a name="state-changes"></a>Zmiany stanu

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>powiadamia składnik o zmianie stanu. Jeśli ma to zastosowanie `StateHasChanged` , wywołanie powoduje, że składnik jest renderowany.

## <a name="handle-incomplete-async-actions-at-render"></a>Obsługuj niekompletne akcje asynchroniczne podczas renderowania

Akcje asynchroniczne wykonane w zdarzeniach cyklu życia mogły nie zostać ukończone przed renderowaniem składnika. Obiekty mogą być `null` lub uzupełniane wypełniane danymi podczas wykonywania metody cyklu życia. Zapewnianie logiki renderowania w celu potwierdzenia, że obiekty są inicjowane. Renderuj zastępcze elementy interfejsu użytkownika (na przykład komunikat ładowania), gdy obiekty `null`są.

W `FetchData` Blazor składniku szablonów program `OnInitializedAsync` został zastąpiony asynchronicznie odbierania danych prognozy (`forecasts`). Gdy `forecasts` tak `null`jest, zostanie wyświetlony komunikat ładowania użytkownika. Po zakończeniu `Task` zwracany przez `OnInitializedAsync` program składnik zostanie przerenderowany ze zaktualizowanym stanem.

*Pages/FetchData. Razor* w Blazor szablonie serwera:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Usuwanie składnika z interfejsem IDisposable

W przypadku zaimplementowania <xref:System.IDisposable>składnika [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) jest wywoływana, gdy składnik zostanie usunięty z interfejsu użytkownika. Poniższy składnik używa `@implements IDisposable` i `Dispose` metody:

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Wywoływanie `Dispose` metody nie jest obsługiwane. `StateHasChanged`może być wywoływana w ramach rozrywania modułu renderowania, dlatego żądanie aktualizacji interfejsu użytkownika nie jest obsługiwane.

Procedury obsługi zdarzeń anulowania subskrypcji z zdarzeń platformy .NET. Poniższe [ Blazor przykłady formularzy](xref:blazor/forms-validation) pokazują, jak odpiąć procedurę obsługi zdarzeń w `Dispose` metodzie:

* Pole prywatne i podejście lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Podejście metody prywatnej

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Obsługa błędów

Aby uzyskać informacje na temat obsługi błędów podczas wykonywania metody cyklu <xref:blazor/handle-errors#lifecycle-methods>życia, zobacz.

## <a name="stateful-reconnection-after-prerendering"></a>Stanowe Ponowne nawiązywanie połączenia po przeprowadzeniu prerenderowania

W aplikacji Blazor serwera, gdy `RenderMode` jest `ServerPrerendered`, składnik jest początkowo renderowany statycznie jako część strony. Gdy przeglądarka nawiąże połączenie z serwerem, składnik jest renderowany *ponownie*, a składnik jest teraz interaktywny. Jeśli istnieje metoda cyklu życia " [OnInitialized {Async}](#component-initialization-methods) " dla inicjowania składnika, metoda jest wykonywana *dwukrotnie*:

* Gdy składnik jest wstępnie renderowany statycznie.
* Po nawiązaniu połączenia z serwerem.

Może to spowodować zauważalną zmianę danych wyświetlanych w interfejsie użytkownika, gdy składnik jest renderowany.

Aby uniknąć podwójnego renderowania w aplikacji Blazor serwerowej:

* Przekaż identyfikator, który może służyć do buforowania stanu podczas wykonywania prerenderowania i pobierania stanu po ponownym uruchomieniu aplikacji.
* Użyj identyfikatora podczas renderowania, aby zapisać stan składnika.
* Użyj identyfikatora po włączeniu, aby pobrać buforowany stan.

Poniższy kod ilustruje aktualizację `WeatherForecastService` w aplikacji serwerowej opartej Blazor na szablonie, która pozwala uniknąć podwójnego renderowania:

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

Aby uzyskać więcej informacji na `RenderMode`temat, <xref:blazor/hosting-model-configuration#render-mode>Zobacz.

## <a name="detect-when-the-app-is-prerendering"></a>Wykryj, kiedy aplikacja jest przedrenderowana

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a>Anulowanie pracy w tle

Składniki często wykonują długotrwałą pracę w tle, taką jak tworzenie wywołań sieciowych<xref:System.Net.Http.HttpClient>() i współdziałanie z bazami danych. Pożądane jest zatrzymanie pracy w tle w celu zapełnienia zasobów systemowych w kilku sytuacjach. Na przykład operacje asynchroniczne w tle nie są automatycznie przerywane, gdy użytkownik przechodzi poza składnik.

Inne powody, dla których elementy robocze w tle mogą wymagać anulowania, to:

* Rozpoczęto wykonywanie zadania w tle z uszkodzonymi danymi wejściowymi lub przetwarzaniem parametrów.
* Bieżący zestaw wykonywanych elementów roboczych w tle musi zostać zastąpiony nowym zestawem elementów roboczych.
* Priorytet aktualnie wykonywanych zadań musi zostać zmieniony.
* Aby można było ponownie wdrożyć je na serwerze, należy zamknąć aplikację.
* Zasoby serwera stają się ograniczone, co wymaga ponownego planowania Backgound elementów roboczych.

Aby zaimplementować w składniku wzorzec pracy z możliwością anulowania w tle:

* Użyj <xref:System.Threading.CancellationTokenSource> i <xref:System.Threading.CancellationToken>.
* Po rozwiązaniu składnika i w dowolnym momencie anulowanie jest wymagane przez ręczne [usunięcie](#component-disposal-with-idisposable) tokenu, wywołaj [CancellationTokenSource. Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) , aby sygnalizować, że działanie w tle powinno być anulowane.
* Po wywołaniu asynchronicznym wywołanie <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na tokenie.

W poniższym przykładzie:

* `await Task.Delay(5000, cts.Token);`reprezentuje długotrwałe działanie asynchroniczne w tle.
* `BackgroundResourceMethod`reprezentuje długotrwałą metodę w tle, która `Resource` nie powinna być uruchamiana, jeśli element zostanie usunięty przed wywołaniem metody.

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
