---
title: ASP.NET Podstawowy Blazor cykl życia
author: guardrex
description: Dowiedz się, jak korzystać z metod cyklu Blazor życia komponentu Razor w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 831f575afa6ce11d06c016d43ecd1bb59d09eab6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218911"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Podstawowy Blazor cykl życia

Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)

Struktura Blazor obejmuje synchroniczne i asynchroniczne metody cyklu życia. Zastępowanie metod cyklu życia w celu wykonywania dodatkowych operacji na komponentach podczas inicjowania i renderowania komponentów.

## <a name="lifecycle-methods"></a>Metody cyklu życia

### <a name="component-initialization-methods"></a>Metody inicjowania komponentów

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> są wywoływane, gdy składnik jest inicjowany po otrzymaniu jego parametrów początkowych ze składnika nadrzędnego. Użyj, `OnInitializedAsync` gdy składnik wykonuje operację asynchronizacyjną i należy odświeżyć po zakończeniu operacji.

W przypadku operacji synchroniczowej `OnInitialized`zastępuj:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Aby wykonać operację asynchronizacyjną, należy zastąpić `OnInitializedAsync` i użyć `await` słowa kluczowego w operacji:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorAplikacje serwera, które [prerender ich zawartość](xref:blazor/hosting-model-configuration#render-mode) wywołać `OnInitializedAsync` dwa **_razy:_**

* Raz, gdy składnik jest początkowo renderowane statycznie jako część strony.
* Po raz drugi, gdy przeglądarka nawiązuje połączenie z serwerem.

Aby zapobiec dwa `OnInitializedAsync` razy uruchomiony kod dewelopera, zobacz [stateful ponownego połączenia po prerendering](#stateful-reconnection-after-prerendering) sekcji.

Podczas Blazor gdy aplikacja Server jest prerendering, niektóre akcje, takie jak wywołanie javascript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało ustanowione. Składniki mogą wymagać renderowania inaczej podczas prerendered. Aby uzyskać więcej informacji, zobacz [Wykrywanie, gdy aplikacja jest prerendering](#detect-when-the-app-is-prerendering) sekcji.

Jeśli są skonfigurowane programy obsługi zdarzeń, odłącz je w dyspozycji. Aby uzyskać więcej informacji, zobacz [usuwanie składnika z IDisposable](#component-disposal-with-idisposable) sekcji.

### <a name="before-parameters-are-set"></a>Przed ustawieniem parametrów

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>ustawia parametry dostarczone przez element nadrzędny komponentu w drzewie renderowania:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>zawiera cały zestaw wartości parametrów za każdym razem, gdy `SetParametersAsync` jest wywoływana.

Domyślna `SetParametersAsync` implementacja ustawia wartość każdej `[Parameter]` właściwości `[CascadingParameter]` z atrybutem lub atrybutem, który ma odpowiednią wartość w pliku `ParameterView`. Parametry, które nie mają odpowiedniej `ParameterView` wartości w pozostają niezmienione.

Jeśli `base.SetParametersAync` nie jest wywoływana, kod niestandardowy można interpretować wartość parametrów przychodzących w dowolny sposób wymagane. Na przykład nie ma wymogu, aby przypisać parametry przychodzące do właściwości w klasie.

Jeśli są skonfigurowane programy obsługi zdarzeń, odłącz je w dyspozycji. Aby uzyskać więcej informacji, zobacz [usuwanie składnika z IDisposable](#component-disposal-with-idisposable) sekcji.

### <a name="after-parameters-are-set"></a>Po ustawieniu parametrów

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> są nazywane:

* Gdy komponent jest inicjowany i otrzymał swój pierwszy zestaw parametrów ze swojego komponentu nadrzędnego.
* Gdy składnik nadrzędny ponownie renderuje i dostarcza:
  * Tylko znane pierwotne typy, których co najmniej jeden parametr uległ zmianie.
  * Wszelkie parametry typu complex. Struktura nie może wiedzieć, czy wartości parametru typizowanego typu complex zostały zmutowane wewnętrznie, więc traktuje ustawiony parametr jako zmieniony.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Praca asynchronizacjowa podczas stosowania parametrów `OnParametersSetAsync` i wartości właściwości musi wystąpić podczas zdarzenia cyklu życia.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Jeśli są skonfigurowane programy obsługi zdarzeń, odłącz je w dyspozycji. Aby uzyskać więcej informacji, zobacz [usuwanie składnika z IDisposable](#component-disposal-with-idisposable) sekcji.

### <a name="after-component-render"></a>Po renderowaniu komponentu

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> są wywoływane po zakończeniu renderowania komponentu. Odwołania do elementów i składników są wypełniane w tym momencie. Ten etap służy do wykonywania dodatkowych kroków inicjowania przy użyciu renderowanych treści, takich jak aktywowanie bibliotek JavaScript innych firm działających na renderowanych elementach DOM.

Parametr `firstRender` dla `OnAfterRenderAsync` `OnAfterRender`i:

* Jest `true` ustawiona na pierwszy raz, że wystąpienie składnika jest renderowany.
* Może służyć do zapewnienia, że praca inicjowania jest wykonywana tylko raz.

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
> Praca asynchronicznego natychmiast po renderowaniu `OnAfterRenderAsync` musi wystąpić podczas zdarzenia cyklu życia.
>
> Nawet jeśli <xref:System.Threading.Tasks.Task> zwrócisz `OnAfterRenderAsync`z programu, struktura nie planuje dalszego cyklu renderowania dla składnika po zakończeniu tego zadania. Ma to na celu uniknięcie nieskończonej pętli renderowania. Różni się od innych metod cyklu życia, które planują kolejny cykl renderowania po zakończeniu zwracanego zadania.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender`i `OnAfterRenderAsync` *nie są wywoływane podczas prerendering na serwerze.*

Jeśli są skonfigurowane programy obsługi zdarzeń, odłącz je w dyspozycji. Aby uzyskać więcej informacji, zobacz [usuwanie składnika z IDisposable](#component-disposal-with-idisposable) sekcji.

### <a name="suppress-ui-refreshing"></a>Pomijanie odświeżania interfejsu użytkownika

Zastądeń, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> aby pominąć odświeżanie interfejsu użytkownika. Jeśli implementacja `true`powróci, interfejs użytkownika jest odświeżany:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`jest wywoływana za każdym razem, gdy składnik jest renderowany.

Nawet `ShouldRender` jeśli jest zastępowany, składnik jest zawsze początkowo renderowane.

## <a name="state-changes"></a>Zmiany stanu

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>powiadamia składnik, że jego stan uległ zmianie. W stosownych `StateHasChanged` przypadkach wywołanie powoduje, że składnik ma być rerendered.

## <a name="handle-incomplete-async-actions-at-render"></a>Obsługa niekompletnych akcji asynchronizowych podczas renderowania

Akcje asynchroniczne wykonywane w zdarzeniach cyklu życia mogły nie zostać ukończone przed renderowaniem składnika. Obiekty mogą `null` być lub niecałkowicie wypełnione danymi podczas wykonywania metody cyklu życia. Podaj logikę renderowania, aby potwierdzić, że obiekty są inicjowane. Renderuj elementy zastępczego interfejsu użytkownika (na przykład `null`wiadomość ładującej) podczas gdy obiekty są .

W `FetchData` składniku Blazor szablonów `OnInitializedAsync` jest zastępowane do asychronously otrzymywać`forecasts`dane prognozy ( ). Gdy `forecasts` `null`jest , wiadomość ładowania jest wyświetlany do użytkownika. Po `Task` zakończeniu `OnInitializedAsync` zwracania składnik jest rerenderowany ze zaktualizowanym stanem.

*Pages/FetchData.brzytwa* w szablonie Blazor serwera:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Utylizacja komponentów z IDisposable

Jeśli składnik implementuje, <xref:System.IDisposable> [Dispose metoda](/dotnet/standard/garbage-collection/implementing-dispose) jest wywoływana, gdy składnik jest usuwany z interfejsu użytkownika. Następujące zastosowania składnika `@implements IDisposable` `Dispose` i metody:

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
> `Dispose` Wywołanie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> nie jest obsługiwane. `StateHasChanged`może być wywoływana jako część rozbiórki modułu renderowania, więc żądanie aktualizacji interfejsu użytkownika w tym momencie nie jest obsługiwane.

Anulowanie obsługi zdarzeń z .NET. Poniższe przykłady [ Blazor formularza](xref:blazor/forms-validation) pokazują, jak odłączyć program obsługi zdarzeń w metodzie: `Dispose`

* Prywatne pole i podejście lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Podejście metody prywatnej

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Obsługa błędów

Aby uzyskać informacje na temat obsługi błędów <xref:blazor/handle-errors#lifecycle-methods>podczas wykonywania metody cyklu życia, zobacz .

## <a name="stateful-reconnection-after-prerendering"></a>Stateful ponownego połączenia po prerendering

W Blazor aplikacji Server, gdy `RenderMode` jest `ServerPrerendered`, składnik jest początkowo renderowane statycznie jako część strony. Gdy przeglądarka nawiązuje połączenie z serwerem, składnik jest renderowany *ponownie,* a składnik jest teraz interaktywny. Jeśli istnieje metoda cyklu życia [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) do inicjowania składnika, metoda jest wykonywana *dwa razy:*

* Gdy składnik jest prerendered statycznie.
* Po nawiązaniu połączenia z serwerem.

Może to spowodować zauważalną zmianę danych wyświetlanych w interfejsie użytkownika, gdy składnik jest ostatecznie renderowany.

Aby uniknąć scenariusza podwójnego renderowania Blazor w aplikacji server:

* Przekaż identyfikator, który może służyć do buforowania stanu podczas prerendering i pobrać stan po ponownym uruchomieniu aplikacji.
* Użyj identyfikatora podczas prerendering, aby zapisać stan składnika.
* Użyj identyfikatora po prerendering pobrać stan buforowane.

Poniższy kod pokazuje `WeatherForecastService` zaktualizowane w Blazor aplikacji serwera opartej na szablonach, która pozwala uniknąć podwójnego renderowania:

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

Aby uzyskać więcej `RenderMode`informacji <xref:blazor/hosting-model-configuration#render-mode>na temat , zobacz .

## <a name="detect-when-the-app-is-prerendering"></a>Wykrywanie, kiedy aplikacja jest prerendering

[!INCLUDE[](~/includes/blazor-prerendering.md)]
