---
title: ASP.NET Core Blazor cykl życia
author: guardrex
description: Dowiedz się, jak korzystać z Razor metod cyklu życia składników w Blazor aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 6e9d2c3180fb9e4c3e5ccc0b6d8e17183f78d698
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109848"
---
# <a name="aspnet-core-blazor-lifecycle"></a>ASP.NET Core Blazor cykl życia

BlazorPlatforma obejmuje metody cyklu życia synchronicznego i asynchronicznego. Zastąp metody cyklu życia, aby wykonać dodatkowe operacje na składnikach podczas inicjowania i renderowania składnika.

Poniższe diagramy ilustrują Blazor cykl życia. Metody cyklu życia są definiowane przy użyciu przykładów w poniższych sekcjach tego artykułu.

Zdarzenia cyklu życia składnika:

1. Jeśli składnik jest renderowany po raz pierwszy w żądaniu:
   * Utwórz wystąpienie składnika.
   * Wykonaj iniekcję właściwości. Uruchom [`SetParametersAsync`](#before-parameters-are-set) .
   * Wywołanie [`OnInitialized{Async}`](#component-initialization-methods) . W przypadku <xref:System.Threading.Tasks.Task> zwrócenia elementu <xref:System.Threading.Tasks.Task> jest oczekiwany, a składnik jest renderowany. Jeśli element <xref:System.Threading.Tasks.Task> nie jest zwracany, składnik jest renderowany.
1. Wywołaj [`OnParametersSet{Async}`](#after-parameters-are-set) i Renderuj składnik. Jeśli <xref:System.Threading.Tasks.Task> jest zwracany z `OnParametersSetAsync` , <xref:System.Threading.Tasks.Task> jest oczekiwany, a następnie składnik jest ponownie renderowany.

![Zdarzenia cyklu życia składnika::: No-Loc (Razor)::: składnik w::: No-Loc (Blazor)::](lifecycle/_static/lifecycle1.png)

Przetwarzanie zdarzeń w Document Object Model (DOM):

1. Procedura obsługi zdarzeń jest uruchamiana.
1. W przypadku <xref:System.Threading.Tasks.Task> zwrócenia elementu <xref:System.Threading.Tasks.Task> jest oczekiwany, a następnie jest renderowany składnik. Jeśli element <xref:System.Threading.Tasks.Task> nie jest zwracany, składnik jest renderowany.

![Przetwarzanie zdarzeń Document Object Model (DOM)](lifecycle/_static/lifecycle2.png)

`Render`Cykl życia:

1. Unikaj dalszych operacji renderowania na składniku:
   * Po pierwszym wykonaniu.
   * Gdy [`ShouldRender`](#suppress-ui-refreshing) jest `false` .
1. Kompiluj porównanie drzewa renderowania (różnica) i Renderuj składnik.
1. Oczekiwanie na zaktualizowanie modelu DOM.
1. Wywołanie [`OnAfterRender{Async}`](#after-component-render) .

![Cykl życia renderowania](lifecycle/_static/lifecycle3.png)

Deweloperzy mogą wykonywać wywołania [`StateHasChanged`](#state-changes) w wyniku. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.

## <a name="lifecycle-methods"></a>Metody cyklu życia

### <a name="before-parameters-are-set"></a>Przed ustawieniem parametrów

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> Ustawia parametry dostarczone przez element nadrzędny składnika w drzewie renderowania lub z parametrów trasy. Zastępując metodę, kod dewelopera może współdziałać bezpośrednio z <xref:Microsoft.AspNetCore.Components.ParameterView> parametrami.

W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> przypisuje `Param` wartość parametru do, `value` Jeśli analizowanie parametru trasy dla programu `Param` zakończyło się pomyślnie. Gdy `value` nie jest `null` , wartość jest wyświetlana przez składnik.

Chociaż [dopasowanie parametrów trasy nie uwzględnia wielkości liter](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> w szablonie trasy są uwzględniane tylko nazwy parametrów z uwzględnieniem wielkości liter. Poniższy przykład jest wymagany do korzystania z `/{Param?}` nie, aby `/{param?}` można było pobrać wartość. Jeśli `/{param?}` jest używana w tym scenariuszu, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> zwraca `false` i `message` nie jest ustawiona na ciąg.

`Pages/SetParametersAsyncExample.razor`:

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> zawiera zestaw wartości parametrów dla składnika, gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> jest wywoływana.

Domyślna implementacja programu <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ustawia wartość każdej właściwości z [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [ `[CascadingParameter]` atrybutem](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) lub, który ma odpowiednią wartość w <xref:Microsoft.AspNetCore.Components.ParameterView> . Parametry, które nie mają odpowiedniej wartości w, <xref:Microsoft.AspNetCore.Components.ParameterView> pozostają bez zmian.

Jeśli [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) nie zostanie wywołana, kod niestandardowy może interpretować wartość parametrów przychodzących w dowolny sposób. Na przykład nie jest wymagane przypisanie parametrów przychodzących do właściwości w klasie.

W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia. Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .

### <a name="component-initialization-methods"></a>Metody inicjujące składniki

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> są wywoływane, gdy składnik zostanie zainicjowany po odebraniu początkowych parametrów z jego składnika nadrzędnego w <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> . 

Użyj <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , gdy składnik wykonuje operację asynchroniczną i powinien być odświeżany po zakończeniu operacji.

W przypadku operacji synchronicznej Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :

```csharp
protected override void OnInitialized()
{
    ...
}
```

Aby wykonać operację asynchroniczną, Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i Użyj [`await`](/dotnet/csharp/language-reference/operators/await) operatora dla operacji:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Serveraplikacje, które dwukrotnie wywołują [swoje wywołanie zawartości](xref:blazor/fundamentals/signalr#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> :

* Gdy składnik jest początkowo renderowany statycznie jako część strony.
* Drugi raz, gdy przeglądarka nawiąże połączenie z serwerem.

Aby zapobiec <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> dwukrotnemu uruchomieniu kodu dewelopera, zapoznaj się z sekcją [ponowne łączenie po prerenderowania](#stateful-reconnection-after-prerendering) .

Podczas gdy Blazor Server aplikacja jest wstępnie renderowana, niektóre akcje, takie jak wywoływanie kodu JavaScript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało nawiązane. Składniki mogą być konieczne w różny sposób, gdy są wstępnie renderowane. Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy aplikacja jest prerenderowana](#detect-when-the-app-is-prerendering) .

W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia. Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .

### <a name="after-parameters-are-set"></a>Po ustawieniu parametrów

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> są wywoływane:

* Po zainicjowaniu składnika w programie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
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
> Asynchroniczna działanie, gdy stosowane są parametry i wartości właściwości, muszą wystąpić podczas <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> zdarzenia cyklu życia.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia. Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .

### <a name="after-component-render"></a>Po renderowania składników

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> są wywoływane po zakończeniu renderowania składnika. Odwołania do elementów i składników są wypełniane w tym momencie. Ten etap służy do wykonywania dodatkowych kroków inicjowania przy użyciu renderowanej zawartości, takiej jak aktywacja bibliotek języka JavaScript innych firm, które działają na renderowanych elementach DOM.

`firstRender`Parametr dla <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :

* Jest ustawiany po `true` raz pierwszy, gdy wystąpienie składnika jest renderowane.
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
> Asynchroniczne działanie natychmiast po wyrenderowaniu musi wystąpić w trakcie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> zdarzenia cyklu życia.
>
> Nawet w przypadku powrotu <xref:System.Threading.Tasks.Task> z programu <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , struktura nie planuje dalszych cykli renderowania dla składnika po zakończeniu tego zadania. Ma to na celu uniknięcie nieskończonej pętli renderowania. Różnią się one od innych metod cyklu życia, które Zaplanuj kolejny cykl renderowania po zakończeniu zwracanego zadania.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *nie są wywoływane podczas procesu renderowania wstępnego na serwerze*. Metody są wywoływane, gdy składnik jest renderowany interaktywnie po zakończeniu renderowania prerenderingu. Gdy aplikacja jest przedrenderowana:

1. Składnik jest wykonywany na serwerze w celu utworzenia statycznego znacznika HTML w odpowiedzi HTTP. W tej fazie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> nie są wywoływane.
1. Kiedy `blazor.server.js` lub `blazor.webassembly.js` uruchamiasz w przeglądarce, składnik jest uruchamiany ponownie w trybie renderowania interaktywnego. Po ponownym uruchomieniu składnika <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **są** wywoływane, ponieważ aplikacja nie znajduje się w fazie renderowania prerenderingu.

W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia. Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .

### <a name="suppress-ui-refreshing"></a>Pomiń odświeżanie interfejsu użytkownika

Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , aby pominąć odświeżanie interfejsu użytkownika. Jeśli implementacja zwraca `true` , interfejs użytkownika zostanie odświeżony:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest wywoływana za każdym razem, gdy składnik jest renderowany.

Nawet jeśli <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest zastępowany, składnik jest zawsze początkowo renderowany.

Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.

## <a name="state-changes"></a>Zmiany stanu

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powiadamia składnik o zmianie stanu. Jeśli ma to zastosowanie, wywołanie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powoduje, że składnik jest renderowany.

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana automatycznie dla <xref:Microsoft.AspNetCore.Components.EventCallback> metod. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling#eventcallback>.

Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.

## <a name="handle-incomplete-async-actions-at-render"></a>Obsługuj niekompletne akcje asynchroniczne podczas renderowania

Akcje asynchroniczne wykonane w zdarzeniach cyklu życia mogły nie zostać ukończone przed renderowaniem składnika. Obiekty mogą być `null` lub uzupełniane wypełniane danymi podczas wykonywania metody cyklu życia. Zapewnianie logiki renderowania w celu potwierdzenia, że obiekty są inicjowane. Renderuj zastępcze elementy interfejsu użytkownika (na przykład komunikat ładowania), gdy obiekty są `null` .

W `FetchData` składniku Blazor szablonów program <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> jest zastępowany asynchronicznie odbieranych danych prognozy ( `forecasts` ). Gdy `forecasts` tak jest `null` , zostanie wyświetlony komunikat ładowania użytkownika. Po `Task` zakończeniu zwracany przez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> program składnik zostanie przerenderowany ze zaktualizowanym stanem.

`Pages/FetchData.razor` w Blazor Server szablonie:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a>Obsługa błędów

Aby uzyskać informacje na temat obsługi błędów podczas wykonywania metody cyklu życia, zobacz <xref:blazor/fundamentals/handle-errors> .

## <a name="stateful-reconnection-after-prerendering"></a>Stanowe Ponowne nawiązywanie połączenia po przeprowadzeniu prerenderowania

W Blazor Server aplikacji, gdy <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> jest <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , składnik jest początkowo renderowany statycznie jako część strony. Gdy przeglądarka nawiąże połączenie z serwerem, składnik jest renderowany *ponownie*, a składnik jest teraz interaktywny. Jeśli [`OnInitialized{Async}`](#component-initialization-methods) istnieje metoda cyklu życia do inicjowania składnika, metoda jest wykonywana *dwukrotnie*:

* Gdy składnik jest wstępnie renderowany statycznie.
* Po nawiązaniu połączenia z serwerem.

Może to spowodować zauważalną zmianę danych wyświetlanych w interfejsie użytkownika, gdy składnik jest renderowany.

Aby uniknąć podwójnego renderowania w Blazor Server aplikacji:

* Przekaż identyfikator, który może służyć do buforowania stanu podczas wykonywania prerenderowania i pobierania stanu po ponownym uruchomieniu aplikacji.
* Użyj identyfikatora podczas renderowania, aby zapisać stan składnika.
* Użyj identyfikatora po włączeniu, aby pobrać buforowany stan.

Poniższy kod ilustruje aktualizację `WeatherForecastService` w aplikacji opartej na szablonie Blazor Server , która pozwala uniknąć podwójnego renderowania:

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

Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , zobacz <xref:blazor/fundamentals/signalr#render-mode> .

## <a name="detect-when-the-app-is-prerendering"></a>Wykryj, kiedy aplikacja jest przedrenderowana

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a>Usuwanie składnika z `IDisposable`

Jeśli składnik implementuje <xref:System.IDisposable> , struktura wywołuje [metodę usuwania](/dotnet/standard/garbage-collection/implementing-dispose) po usunięciu składnika z interfejsu użytkownika, w którym można wydać niezarządzane zasoby. Usuwanie może odbywać się w dowolnym momencie, w tym podczas [inicjowania składnika](#component-initialization-methods). Następujący składnik implementuje <xref:System.IDisposable> z [`@implements`](xref:mvc/views/razor#implements) Razor dyrektywą:

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

Jeśli obiekt wymaga usunięcia, można użyć wyrażenia lambda do usuwania obiektu, gdy <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> jest wywoływana:

`Pages/CounterWithTimerDisposal.razor`:

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

Poprzedni przykład jest wyświetlany w <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> .

W przypadku asynchronicznych zadań usuwania Użyj `DisposeAsync` zamiast <xref:System.IDisposable.Dispose> :

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> Wywoływanie metody <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> `Dispose` nie jest obsługiwane. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> może być wywoływana w ramach rozrywania modułu renderowania, dlatego żądanie aktualizacji interfejsu użytkownika nie jest obsługiwane.

Procedury obsługi zdarzeń anulowania subskrypcji z zdarzeń platformy .NET. Poniższe przykłady [ Blazor formularzy](xref:blazor/forms-validation) pokazują, jak anulować subskrypcję programu obsługi zdarzeń w `Dispose` metodzie:

* Pole prywatne i podejście lambda

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

* Podejście metody prywatnej

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

Gdy są używane [anonimowe funkcje](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), metody lub wyrażenia, nie trzeba implementować <xref:System.IDisposable> i anulować subskrybowania delegatów. Niepowodzenie anulowania subskrypcji delegata jest jednak problemem, **gdy obiekt ujawniający zdarzenie wyrejestruje okres istnienia elementu delegowanego**. W takim przypadku przyczyną przecieku pamięci jest fakt, że zarejestrowany delegat zachowuje oryginalny obiekt. W związku z tym należy stosować następujące podejścia tylko wtedy, gdy wiadomo, że delegat zdarzenia szybko usuwa. W razie wątpliwości dotyczących okresu istnienia obiektów, które wymagają usunięcia, zasubskrybuj metodę delegata i prawidłowo Usuń delegata, jak pokazano w poprzednich przykładach.

* Anonimowe podejście metody lambda (jawne usuwanie nie jest wymagane)

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

* Anonimowe podejście wyrażenia lambda (jawne usuwanie nie jest wymagane)

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  Pełny przykład powyższego kodu z anonimowymi wyrażeniami lambda pojawia się w <xref:blazor/forms-validation#validator-components> .

Aby uzyskać więcej informacji, zobacz [Oczyszczanie zasobów niezarządzanych](/dotnet/standard/garbage-collection/unmanaged) i tematy, które po nich wykonują, przy wdrażaniu `Dispose` `DisposeAsync` metod i.

## <a name="cancelable-background-work"></a>Anulowanie pracy w tle

Składniki często wykonują długotrwałą pracę w tle, taką jak tworzenie wywołań sieciowych ( <xref:System.Net.Http.HttpClient> ) i współdziałanie z bazami danych. Pożądane jest zatrzymanie pracy w tle w celu zapełnienia zasobów systemowych w kilku sytuacjach. Na przykład operacje asynchroniczne w tle nie są automatycznie przerywane, gdy użytkownik przechodzi poza składnik.

Inne powody, dla których elementy robocze w tle mogą wymagać anulowania, to:

* Rozpoczęto wykonywanie zadania w tle z uszkodzonymi danymi wejściowymi lub przetwarzaniem parametrów.
* Bieżący zestaw wykonywanych elementów roboczych w tle musi zostać zastąpiony nowym zestawem elementów roboczych.
* Priorytet aktualnie wykonywanych zadań musi zostać zmieniony.
* Aby można było ponownie wdrożyć je na serwerze, należy zamknąć aplikację.
* Zasoby serwera są ograniczone, co wymaga ponownego zaplanowania elementów roboczych w tle.

Aby zaimplementować w składniku wzorzec pracy z możliwością anulowania w tle:

* Użyj <xref:System.Threading.CancellationTokenSource> i <xref:System.Threading.CancellationToken> .
* Po [usunięciu składnika](#component-disposal-with-idisposable) i w dowolnym momencie anulowanie jest wymagane przez ręczne unieważnienie tokenu, wywołanie [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) do sygnału, że działanie w tle powinno być anulowane.
* Po wywołaniu asynchronicznym wywołanie <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na tokenie.

W poniższym przykładzie:

* `await Task.Delay(5000, cts.Token);` reprezentuje długotrwałe działanie asynchroniczne w tle.
* `BackgroundResourceMethod` reprezentuje długotrwałą metodę w tle, która nie powinna być uruchamiana, jeśli element `Resource` zostanie usunięty przed wywołaniem metody.

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

## <a name="blazor-server-reconnection-events"></a>Blazor Server zdarzenia ponownego połączenia

Zdarzenia cyklu życia składnika omówione w tym artykule działają niezależnie od [ Blazor Server programów obsługi zdarzeń ponownego połączenia](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui). Gdy Blazor Server aplikacja utraci SignalR połączenie z klientem, wszystkie aktualizacje interfejsu użytkownika są przerywane. Aktualizacje interfejsu użytkownika są wznawiane po ponownym nawiązaniu połączenia. Aby uzyskać więcej informacji na temat zdarzeń i konfiguracji obsługi obwodów, zobacz <xref:blazor/fundamentals/signalr> .
