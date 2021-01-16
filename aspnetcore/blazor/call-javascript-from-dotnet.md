---
title: Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak wywoływać funkcje języka JavaScript z metod .NET w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 2502f43f4eaf245996827f704462ec340bbb8e07
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252542"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core Blazor

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm)i [Luke](https://github.com/guardrex) Latham

BlazorAplikacja może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript. Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie *js Interop*).

W tym artykule opisano wywoływanie funkcji języka JavaScript z platformy .NET. Aby uzyskać informacje na temat wywoływania metod .NET w języku JavaScript, zobacz <xref:blazor/call-dotnet-from-javascript> .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Aby wywołać kod JavaScript z platformy .NET, użyj <xref:Microsoft.JSInterop.IJSRuntime> abstrakcji. Aby wystawić wywołania programu JS Interop, wstrzyknąć <xref:Microsoft.JSInterop.IJSRuntime> streszczenie w składniku. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> przyjmuje identyfikator funkcji języka JavaScript, która ma zostać wywołana wraz z dowolną liczbą argumentów do serializacji JSON. Identyfikator funkcji jest względny w stosunku do zakresu globalnego ( `window` ). Jeśli chcesz wywołać `window.someScope.someFunction` , identyfikator to `someScope.someFunction` . Nie ma potrzeby rejestrowania funkcji przed jej wywołaniem. Zwracanym typem `T` musi być również kod JSON możliwy do serializacji. `T` powinien być zgodny z typem .NET, który najlepiej jest mapowany do zwracanego typu JSON.

Funkcje języka JavaScript, które zwracają [obietnicę](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , są wywoływane z <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> . `InvokeAsync` odpakuje obietnicę i zwraca wartość oczekiwaną przez obietnicę.

W przypadku Blazor Server aplikacji z włączoną funkcją prerenderowania Wywoływanie kodu JavaScript nie jest możliwe podczas początkowego wstępnego renderowania. Wywołania międzyoperacyjne języka JavaScript muszą zostać odroczone do momentu ustanowienia połączenia z przeglądarką. Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy Blazor Server aplikacja jest prerenderowana](#detect-when-a-blazor-server-app-is-prerendering) .

Poniższy przykład jest oparty na [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) języku JavaScript. W przykładzie pokazano, jak wywołać funkcję języka JavaScript z metody języka C#, która odciąża wymóg od kodu dewelopera do istniejącego interfejsu API języka JavaScript. Funkcja JavaScript akceptuje tablicę bajtową z metody C#, dekoduje tablicę i zwraca tekst do składnika do wyświetlenia.

Wewnątrz `<head>` elementu `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ), podaj funkcję języka JavaScript, która używa `TextDecoder` do dekodowania przekazaną tablicę i zwracają zdekodowaną wartość:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Kod JavaScript, taki jak kod przedstawiony w powyższym przykładzie, można również załadować z pliku JavaScript ( `.js` ) z odwołaniem do pliku skryptu:

```html
<script src="exampleJsInterop.js"></script>
```

Następujący składnik:

* Wywołuje `convertArray` funkcję JavaScript przy użyciu `JS` przycisku składnika ( **`Convert Array`** ).
* Po wywołaniu funkcji języka JavaScript przenoszona tablica jest konwertowana na ciąg. Ciąg jest zwracany do składnika do wyświetlenia.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Aby użyć <xref:Microsoft.JSInterop.IJSRuntime> abstrakcji, należy zastosować jedną z następujących metod:

* Wstrzyknąć <xref:Microsoft.JSInterop.IJSRuntime> streszczenie do Razor składnika ( `.razor` ):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  W `<head>` elemencie `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ), podaj `handleTickerChanged` funkcję języka JavaScript. Funkcja jest wywoływana z <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> i nie zwraca wartości:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Wstrzyknąć <xref:Microsoft.JSInterop.IJSRuntime> streszczenie do klasy ( `.cs` ):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  W `<head>` elemencie `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ), podaj `handleTickerChanged` funkcję języka JavaScript. Funkcja jest wywoływana z `JS.InvokeAsync` i zwraca wartość:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* W przypadku generowania zawartości dynamicznej przy użyciu [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)należy użyć `[Inject]` atrybutu:

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

W aplikacji przykładowej po stronie klienta, która jest dołączona do tego tematu, dostępne są dwie funkcje języka JavaScript, które współdziałają z modelem DOM, aby odbierać dane wejściowe użytkownika i wyświetlać komunikat powitalny:

* `showPrompt`: Generuje monit o zaakceptowanie danych wprowadzonych przez użytkownika (nazwę użytkownika) i zwraca nazwę obiektu wywołującego.
* `displayWelcome`: Przypisuje Komunikat powitalny od wywołującego do obiektu DOM z `id` `welcome` .

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Umieść `<script>` tag odwołujący się do pliku JavaScript w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

Nie umieszczaj `<script>` znacznika w pliku składnika, ponieważ `<script>` nie można dynamicznie zaktualizować znacznika.

Metody .NET współdziałają z funkcją JavaScript w `exampleJsInterop.js` pliku przez wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .

<xref:Microsoft.JSInterop.IJSRuntime>Abstrakcja jest asynchroniczna, aby umożliwić obsługę Blazor Server scenariuszy. Jeśli aplikacja jest Blazor WebAssembly aplikacją i chcesz wywołać funkcję JavaScript synchronicznie, downcast do <xref:Microsoft.JSInterop.IJSInProcessRuntime> wywołania i <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> zamiast tego. Zalecamy, aby większość bibliotek międzyoperacyjnych JS używała asynchronicznych interfejsów API, aby upewnić się, że biblioteki są dostępne we wszystkich scenariuszach.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Aby włączyć izolację JavaScript w standardowych [modułach języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), zobacz sekcję [ Blazor izolacja kodu JavaScript i odwołania do obiektów](#blazor-javascript-isolation-and-object-references) .

::: moniker-end

Przykładowa aplikacja zawiera składnik demonstrujący międzyoperacyjność JS. Składnik:

* Odbiera dane wprowadzane przez użytkownika za pośrednictwem wiersza polecenia języka JavaScript.
* Zwraca tekst do składnika do przetworzenia.
* Wywołuje drugą funkcję języka JavaScript, która współdziała z modelem DOM, aby wyświetlić komunikat powitalny.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).

1. Gdy `TriggerJsPrompt` jest wykonywane, wybierając **`Trigger JavaScript Prompt`** przycisk składnika, `showPrompt` Funkcja JavaScript dostępna w `wwwroot/exampleJsInterop.js` pliku jest wywoływana.
1. `showPrompt`Funkcja akceptuje dane wejściowe użytkownika (nazwę użytkownika), które są kodowane w formacie HTML i zwracane do składnika. Składnik przechowuje nazwę użytkownika w zmiennej lokalnej, `name` .
1. Ciąg przechowywany w programie `name` jest zawarty w komunikacie powitalnym, który jest przesyłany do funkcji języka JavaScript, `displayWelcome` która renderuje Komunikat powitalny do znacznika nagłówka.

## <a name="call-a-void-javascript-function"></a>Wywoływanie funkcji języka JavaScript typu void

Użyj <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> dla następujących:

* Funkcje języka JavaScript zwracające [wartość void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) lub [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).
* Jeśli program .NET nie jest wymagany do odczytu wyniku wywołania JavaScript.

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Wykryj, kiedy Blazor Server aplikacja jest renderowana
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a>Przechwyć odwołania do elementów

Niektóre scenariusze międzyoperacyjności JS wymagają odwołań do elementów HTML. Na przykład Biblioteka interfejsu użytkownika może wymagać odwołania do elementu dla inicjalizacji lub może być konieczne wywołanie interfejsów API, takich jak `focus` lub `play` .

Przechwyć odwołania do elementów HTML w składniku, korzystając z następującej metody:

* Dodaj `@ref` atrybut do elementu HTML.
* Zdefiniuj pole typu, <xref:Microsoft.AspNetCore.Components.ElementReference> którego nazwa pasuje do wartości `@ref` atrybutu.

Poniższy przykład pokazuje przechwytywanie odwołania do `username` `<input>` elementu:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Użyj odwołania do elementu, aby zmodyfikować zawartość pustego elementu, który nie współdziała z Blazor . Ten scenariusz jest przydatny, gdy interfejs API innej firmy dostarcza zawartość do elementu. Ponieważ Blazor nie współdziała z elementem, nie ma możliwości konfliktu między Blazor reprezentacją elementu a modelem dom.
>
> W poniższym przykładzie jest *niebezpieczne* do mutacji zawartości listy nieuporządkowanej ( `ul` ), ponieważ Blazor współdziała z modelem dom w celu wypełnienia elementów listy elementu ( `<li>` ):
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Jeśli element JS Interop przyniesie zawartość elementu `MyList` i Blazor podejmuje próbę zastosowania różnic do elementu, różnice nie będą zgodne z modelem dom.

<xref:Microsoft.AspNetCore.Components.ElementReference>Jest przenoszona przez kod JavaScript za pośrednictwem międzyoperacyjnego kodu js. Kod JavaScript odbiera `HTMLElement` wystąpienie, które może być używane z normalnymi interfejsami API modelu DOM. Na przykład poniższy kod definiuje metodę rozszerzenia .NET, która umożliwia wysłanie kliknięcia myszą do elementu:

`exampleJsInterop.js`:

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Użyj [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) w kodzie C#, aby skoncentrować się na elemencie, który jest wbudowanym Blazor strukturą i współpracuje z odwołaniami do elementów.

::: moniker-end

Aby wywołać funkcję języka JavaScript, która nie zwraca wartości, użyj <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> . Poniższy kod wyzwala zdarzenie po stronie klienta `Click` , wywołując poprzednią funkcję JavaScript z przechwyconą <xref:Microsoft.AspNetCore.Components.ElementReference> :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

Aby użyć metody rozszerzenia, Utwórz statyczną metodę rozszerzenia, która odbiera <xref:Microsoft.JSInterop.IJSRuntime> wystąpienie:

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

`clickElement`Metoda jest wywoływana bezpośrednio dla obiektu. W poniższym przykładzie przyjęto założenie, że `TriggerClickEvent` Metoda jest dostępna z `JsInteropClasses` przestrzeni nazw:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> `exampleButton`Zmienna jest wypełniana tylko po wyrenderowaniu składnika. W przypadku przekazanie niewypełnionego <xref:Microsoft.AspNetCore.Components.ElementReference> kodu JavaScript kod JavaScript otrzymuje wartość `null` . Aby manipulować odwołaniami do elementów po zakończeniu renderowania składnika, użyj [ `OnAfterRenderAsync` `OnAfterRender` metody lub cyklu życia składnika](xref:blazor/components/lifecycle#after-component-render).

Podczas pracy z typami ogólnymi i zwracają wartość, użyj <xref:System.Threading.Tasks.ValueTask%601> :

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` jest wywoływana bezpośrednio na obiekcie z typem. W poniższym przykładzie przyjęto założenie, że `GenericMethod` jest dostępny z `JsInteropClasses` przestrzeni nazw:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Elementy odniesienia między składnikami

<xref:Microsoft.AspNetCore.Components.ElementReference>Nie można przekazywać między składnikami, ponieważ:

* Wystąpienie jest gwarantowane, że istnieje tylko po wyrenderowaniu składnika, który jest w trakcie lub po wykonaniu <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> metody składnika.
* Element <xref:Microsoft.AspNetCore.Components.ElementReference> to a [`struct`](/csharp/language-reference/builtin-types/struct) , którego nie można przesłać jako [parametru składnika](xref:blazor/components/index#component-parameters).

Aby składnik nadrzędny mógł udostępnić odwołanie do elementu innym składnikom, składnik nadrzędny może:

* Zezwalaj składnikom podrzędnym na rejestrowanie wywołań zwrotnych.
* Wywołaj zarejestrowane wywołania zwrotne podczas <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> zdarzenia z odwołaniem do elementu. Pośrednio takie podejście umożliwia składnikom podrzędnym współdziałanie z odwołaniem do elementu nadrzędnego.

Poniższy Blazor WebAssembly przykład ilustruje podejście.

W programie `<head>` z `wwwroot/index.html` :

```html
<style>
    .red { color: red }
</style>
```

W programie `<body>` z `wwwroot/index.html` :

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor` (składnik nadrzędny):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).

`Shared/SurveyPrompt.razor` (składnik podrzędny):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

`Shared/SurveyPrompt.razor.cs`:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).

## <a name="harden-js-interop-calls"></a>Zabezpieczenia wywołań międzyoperacyjnych w ramach funkcjonalności JS

Usługa JS Interop może zakończyć się niepowodzeniem z powodu błędów sieci i powinna być traktowana jako niezawodna. Domyślnie Blazor Server aplikacja przeprowadzi czas wywołań międzyoperacyjnych js na serwerze po jednej minucie. Jeśli aplikacja może tolerować bardziej agresywny limit czasu, należy ustawić limit czasu przy użyciu jednej z następujących metod:

* Globalnie w programie `Startup.ConfigureServices` Określ limit czasu:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Dla wywołania w kodzie składnika pojedyncze wywołanie może określać limit czasu:

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Więcej informacji o wyczerpaniu zasobów znajduje się w temacie <xref:blazor/security/server/threat-mitigation> .

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Unikaj cyklicznych odwołań do obiektów

Obiekty, które zawierają odwołania cykliczne, nie mogą być serializowane na kliencie dla obu:

* Wywołania metody .NET.
* Wywołania metody JavaScript z języka C#, gdy typem zwracanym są odwołania cykliczne.

Aby uzyskać więcej informacji, zobacz [odwołania cykliczne nie są obsługiwane, zrób dwa (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor Izolacja kodu JavaScript i odwołania do obiektów

Blazor Włącza izolację JavaScript w standardowych [modułach języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). Izolacja JavaScript zapewnia następujące korzyści:

* Zaimportowana wartość JavaScript nie jest już zanieczyszczana globalną przestrzenią nazw.
* Odbiorcy biblioteki i składników nie są zobowiązani do zaimportowania powiązanego języka JavaScript.

Na przykład poniższy moduł JavaScript eksportuje funkcję JavaScript do wyświetlania monitu przeglądarki:

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

Dodaj poprzedni moduł JavaScript do biblioteki .NET jako statyczny element zawartości sieci Web ( `wwwroot/exampleJsInterop.js` ), a następnie zaimportuj moduł do kodu platformy .NET przy użyciu <xref:Microsoft.JSInterop.IJSRuntime> usługi. Usługa jest wstrzykiwana jako `js` (niepokazywana) dla następującego przykładu:

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

`import`Identyfikator w poprzednim przykładzie jest specjalnym identyfikatorem używanym specjalnie do importowania modułu JavaScript. Określ moduł przy użyciu jego stabilnej statycznej ścieżki zasobów sieci Web: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` . Segment ścieżki dla bieżącego katalogu ( `./` ) jest wymagany, aby można było utworzyć poprawną statyczną ścieżkę zasobu do pliku JavaScript. Symbol zastępczy `{LIBRARY NAME}` jest nazwą biblioteki. Symbol zastępczy `{PATH UNDER WWWROOT}` jest ścieżką do skryptu w sekcji `wwwroot` .

<xref:Microsoft.JSInterop.IJSRuntime> Importuje moduł jako `IJSObjectReference` , który reprezentuje odwołanie do obiektu JavaScript z kodu platformy .NET. Użyj `IJSObjectReference` do wywołania wyeksportowanych funkcji języka JavaScript z modułu:

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` reprezentuje odwołanie do obiektu JavaScript, którego funkcje mogą być wywoływane synchronicznie.

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>Korzystanie z bibliotek języka JavaScript, które renderują interfejs użytkownika (elementy DOM)

Czasami warto używać bibliotek języka JavaScript, które tworzą widoczne elementy interfejsu użytkownika w modelu DOM przeglądarki. Na pierwszy rzut oka może wydawać się trudne, ponieważ Blazor system różnicowania polega na kontroli nad drzewem elementów Dom i uruchamianiu do błędów, jeśli jakiś kod zewnętrzny przydzieli drzewo Dom i unieważnia swój mechanizm do zastosowania różnic. Nie jest to Blazor specyficzne ograniczenie. To samo wyzwanie występuje w przypadku wszystkich opartych na różnicach struktur interfejsu użytkownika.

Na szczęście proste osadzanie wygenerowanego zewnętrznie interfejsu użytkownika w Blazor interfejsie użytkownika składnika jest niezawodne. Zalecaną techniką jest `.razor` utworzenie pustego elementu przez kod składnika (plik). W odniesieniu do Blazor systemu różnicowego, element jest zawsze pusty, dlatego moduł renderowania nie odnosi się do elementu i zamiast niego opuszcza jego zawartość. Dzięki temu można bezpiecznie wypełnić element dowolną zewnętrznie zarządzaną zawartością.

Poniższy przykład demonstruje koncepcję. W `if` instrukcji when `firstRender` `true` , zrób coś z `myElement` . Na przykład Wywołaj zewnętrzną bibliotekę JavaScript w celu jej wypełnienia. Blazor pozostawia zawartość elementu tylko do momentu usunięcia tego składnika. Po usunięciu składnika zostanie również usunięta cała poddrzewo DOM składnika.

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

Jako bardziej szczegółowy przykład rozważmy poniższy składnik, który renderuje interaktywną mapę przy użyciu [interfejsów API MapBox typu open source](https://www.mapbox.com/):

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

Odpowiedni moduł JavaScript, który powinien być umieszczony w `wwwroot/mapComponent.js` , jest następujący:

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

W poprzednim przykładzie Zastąp ciąg `{ACCESS TOKEN}` prawidłowym tokenem dostępu, z którego można uzyskać dostęp https://account.mapbox.com .

Aby wygenerować poprawne style, Dodaj następujący tag arkusza stylów do strony HTML hosta ( `index.html` lub `_Host.cshtml` ):

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

W powyższym przykładzie jest tworzony interaktywny interfejs użytkownika mapy, w którym użytkownik:

* Można przeciągnąć, aby przewinąć lub powiększyć.
* Kliknij przycisk przyciski, aby przejść do wstępnie zdefiniowanych lokalizacji.

![Mapa MapBox ulica Tokio, Japonia z przyciskami do wyboru Bristol, Zjednoczone Królestwo i Tokio, Japonia](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

Najważniejsze kwestie, które należy zrozumieć:

 * Wartość `<div>` with `@ref="mapElement"` ma wartość pustą Blazor . Z tego względu `mapbox-gl.js` można bezpiecznie wypełnić i zmodyfikować jego zawartość w czasie. Tej techniki można użyć z dowolną biblioteką języka JavaScript, która renderuje interfejs użytkownika. Można nawet osadzić składniki z struktury SPA w języku JavaScript innej firmy w Blazor składnikach programu, o ile nie spróbują się dowiedzieć i modyfikować innych części strony. *Nie* jest bezpieczny dla zewnętrznego kodu JavaScript, aby modyfikować elementy, które nie są Blazor traktowane jako puste.
 * Korzystając z tego podejścia, należy wziąć pod uwagę reguły dotyczące sposobu Blazor zachowywania lub niszczenia elementów dom. W poprzednim przykładzie składnik bezpiecznie obsługuje przycisk kliknij zdarzenia i aktualizuje istniejące wystąpienie mapy, ponieważ elementy DOM są zachowywane w miarę możliwości domyślnie. Jeśli renderuje listę elementów mapy z wewnątrz `@foreach` pętli, chcesz użyć, `@key` Aby zapewnić zachowywanie wystąpień składników. W przeciwnym razie zmiany w danych listy mogą spowodować wystąpienie składników, aby zachować stan poprzednich wystąpień w niepożądany sposób. Aby uzyskać więcej informacji, zobacz [Używanie @key do zachowywania elementów i składników](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).

Ponadto w poprzednim przykładzie pokazano, jak można hermetyzować logikę JavaScript i zależności w module ES6 i ładować ją dynamicznie przy użyciu `import` identyfikatora. Aby uzyskać więcej informacji, zobacz temat [izolacja JavaScript i odwołania do obiektów](#blazor-javascript-isolation-and-object-references).

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a>Limity rozmiaru dla wywołań międzyoperacyjnych w JS

W programie Blazor WebAssembly platforma nie nakłada limitu rozmiaru danych wejściowych i wyjściowych w programie js.

W programie Blazor Server wywołania programu js Interop są ograniczone przez maksymalny SignalR rozmiar przychodzących komunikatów dozwolony dla metod koncentratora, które są wymuszane przez <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (domyślnie: 32 KB). Program JS do SignalR komunikatów programu .NET większych niż <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> Zgłoś błąd. Platforma nie nakłada limitu rozmiaru SignalR komunikatu z centrum na klienta. Aby uzyskać więcej informacji, zobacz <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.
  
## <a name="js-modules"></a>Moduły JS

W przypadku izolacji JS program JS współdziała z domyślną obsługą języka [ECMAScript (](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) [model ECMAScript](https://tc39.es/ecma262/#sec-modules)) w przeglądarce.

## <a name="unmarshalled-js-interop"></a>Nieskierowany międzyoperacyjna JS

Blazor WebAssembly składniki mogą mieć niską wydajność, gdy obiekty .NET są serializowane pod kątem współdziałania z JS i jeden z następujących warunków jest spełniony:

* Duże ilości obiektów .NET są szybko serializowane. Przykład: wywołania międzyoperacyjnego JS są wykonywane na podstawie przeniesienia urządzenia wejściowego, takiego jak wirowanie kółka myszy.
* Duże obiekty .NET lub wiele obiektów .NET muszą być serializowane dla międzyoperacyjnego JS. Przykład: wywołania międzyoperacyjne JS wymagają serializowania dziesiątek plików.

<xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> reprezentuje odwołanie do obiektu JavaScript, którego funkcje mogą być wywoływane bez nakładu na Serializowanie danych platformy .NET.

W poniższym przykładzie:

* [Struktura](/dotnet/csharp/language-reference/builtin-types/struct) zawierająca ciąg i liczbę całkowitą są przesyłane do kodu JavaScript.
* Funkcje języka JavaScript przetwarzają dane i zwracają wartość logiczną lub ciąg do obiektu wywołującego.
* Ciąg JavaScript nie jest bezpośrednio konwertowany do obiektu platformy .NET `string` . `unmarshalledFunctionReturnString`Funkcja wywołuje funkcję `BINDING.js_string_to_mono_string` do zarządzania konwersją ciągu JavaScript.

> [!NOTE]
> Poniższe przykłady nie są typowymi przypadkami użycia w tym scenariuszu, ponieważ [Struktura](/dotnet/csharp/language-reference/builtin-types/struct) przeniesiona do języka JavaScript nie powoduje niskiej wydajności składników. W przykładzie jest wykorzystywany mały obiekt tylko w celu przedstawienia koncepcji przekazywania nieserializowanych danych platformy .NET.

Zawartość `<script>` bloku w `wwwroot/index.html` lub zewnętrznym pliku JavaScript, do którego odwołuje się `wwwroot/index.html` :

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> `js_string_to_mono_string`Nazwa funkcji, zachowanie i istnienie mogą ulec zmianie w przyszłej wersji platformy .NET. Na przykład:
>
> * Prawdopodobnie zmieniono nazwę funkcji.
> * Sama funkcja może zostać usunięta na korzyść automatycznej konwersji ciągów przez strukturę.

`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop` ):

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

Jeśli `IJSUnmarshalledObjectReference` wystąpienie nie zostanie usunięte w kodzie C#, można je usunąć w języku JavaScript. Następująca `dispose` Funkcja usuwa odwołanie do obiektu w przypadku wywołania z JavaScript:

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

Typy tablic mogą być konwertowane z obiektów JavaScript do obiektów .NET przy użyciu `js_typed_array_to_array` , ale tablica JavaScript musi być tablicą z określonym typem. Tablice z JavaScript mogą być odczytywane w kodzie C# jako tablica obiektów .NET ( `object[]` ).

Inne typy danych, takie jak tablice ciągów, można przekonwertować, ale wymagają utworzenia nowego obiektu tablicy mono ( `mono_obj_array_new` ) i ustawienia jego wartości ( `mono_obj_array_set` ).

> [!WARNING]
> Funkcje języka JavaScript udostępniane przez Blazor platformę, takie jak `js_typed_array_to_array` , `mono_obj_array_new` i `mono_obj_array_set` , podlegają zmianom nazw, zmianom behawioralnym lub usunięciu w przyszłych wersjach platformy .NET.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent. Razor — przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałąź wydania)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
