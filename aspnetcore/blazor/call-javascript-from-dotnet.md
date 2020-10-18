---
title: Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak wywoływać funkcje języka JavaScript z metod .NET w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: a7ba41501b856482c8fcf7efa8e1d78857020bf5
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113767"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core Blazor

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)i [Luke](https://github.com/guardrex) Latham

BlazorAplikacja może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript. Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie*js Interop*).

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

* Wywołuje `convertArray` funkcję JavaScript przy użyciu `JSRuntime` przycisku składnika ( **`Convert Array`** ).
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

  W `<head>` elemencie `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ), podaj `handleTickerChanged` funkcję języka JavaScript. Funkcja jest wywoływana z `JSRuntime.InvokeAsync` i zwraca wartość:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* W przypadku generowania zawartości dynamicznej przy użyciu [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)należy użyć `[Inject]` atrybutu:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

W aplikacji przykładowej po stronie klienta, która jest dołączona do tego tematu, dostępne są dwie funkcje języka JavaScript, które współdziałają z modelem DOM, aby odbierać dane wejściowe użytkownika i wyświetlać komunikat powitalny:

* `showPrompt`: Generuje monit o zaakceptowanie danych wprowadzonych przez użytkownika (nazwę użytkownika) i zwraca nazwę obiektu wywołującego.
* `displayWelcome`: Przypisuje Komunikat powitalny od wywołującego do obiektu DOM z `id` `welcome` .

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Umieść `<script>` tag odwołujący się do pliku JavaScript w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

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
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
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
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

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

W odniesieniu do kodu platformy .NET jest <xref:Microsoft.AspNetCore.Components.ElementReference> to nieprzezroczyste dojście. *Jedyną* czynnością, którą można wykonać, <xref:Microsoft.AspNetCore.Components.ElementReference> jest przekazanie jej do kodu JavaScript za pośrednictwem międzyoperacyjnego js. Gdy to zrobisz, kod po stronie JavaScript odbiera `HTMLElement` wystąpienie, które może być używane z normalnymi interfejsami API modelu DOM.

Na przykład poniższy kod definiuje metodę rozszerzenia .NET, która umożliwia ustawienie fokusu na elemencie:

`exampleJsInterop.js`:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Aby wywołać funkcję języka JavaScript, która nie zwraca wartości, użyj <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> . Poniższy kod ustawia fokus na wejściu do nazwy użytkownika, wywołując poprzednią funkcję JavaScript z przechwyconą <xref:Microsoft.AspNetCore.Components.ElementReference> :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Aby użyć metody rozszerzenia, Utwórz statyczną metodę rozszerzenia, która odbiera <xref:Microsoft.JSInterop.IJSRuntime> wystąpienie:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus`Metoda jest wywoływana bezpośrednio dla obiektu. W poniższym przykładzie przyjęto założenie, że `Focus` Metoda jest dostępna z `JsInteropClasses` przestrzeni nazw:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> `username`Zmienna jest wypełniana tylko po wyrenderowaniu składnika. W przypadku przekazanie niewypełnionego <xref:Microsoft.AspNetCore.Components.ElementReference> kodu JavaScript kod JavaScript otrzymuje wartość `null` . Aby manipulować odwołaniami do elementów po zakończeniu renderowania składnika (aby ustawić początkowy fokus w elemencie), użyj [ `OnAfterRenderAsync` `OnAfterRender` metody lub cyklu życia składnika](xref:blazor/components/lifecycle#after-component-render).

Podczas pracy z typami ogólnymi i zwracają wartość, użyj <xref:System.Threading.Tasks.ValueTask%601> :

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` jest wywoływana bezpośrednio na obiekcie z typem. W poniższym przykładzie przyjęto założenie, że `GenericMethod` jest dostępny z `JsInteropClasses` przestrzeni nazw:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Elementy odniesienia między składnikami

Element <xref:Microsoft.AspNetCore.Components.ElementReference> jest gwarantowany tylko w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> metodzie składnika (i odwołania do elementu `struct` ), dlatego nie można przekazywać odwołania do elementu między składnikami.

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
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Więcej informacji o wyczerpaniu zasobów znajduje się w temacie <xref:blazor/security/server/threat-mitigation> .

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Unikaj cyklicznych odwołań do obiektów

Obiekty, które zawierają odwołania cykliczne, nie mogą być serializowane na kliencie dla obu:

* Wywołania metody .NET.
* Wywołania metody JavaScript z języka C#, gdy typem zwracanym są odwołania cykliczne.

Aby uzyskać więcej informacji, zobacz następujące problemy:

* [Odwołania cykliczne nie są obsługiwane, zrób dwa (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Propozycja: Dodawanie mechanizmu do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/30820)

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

Dodaj poprzedni moduł JavaScript do biblioteki .NET jako statyczny element zawartości sieci Web ( `wwwroot/exampleJsInterop.js` ), a następnie zaimportuj moduł do kodu platformy .NET przy użyciu <xref:Microsoft.JSInterop.IJSRuntime> usługi. Usługa jest wstrzykiwana jako `jsRuntime` (niepokazywana) dla następującego przykładu:

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

`import`Identyfikator w poprzednim przykładzie jest specjalnym identyfikatorem używanym specjalnie do importowania modułu JavaScript. Określ moduł przy użyciu jego stabilnej statycznej ścieżki zasobów sieci Web: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` . Symbol zastępczy `{LIBRARY NAME}` jest nazwą biblioteki. Symbol zastępczy `{PATH UNDER WWWROOT}` jest ścieżką do skryptu w sekcji `wwwroot` .

<xref:Microsoft.JSInterop.IJSRuntime> Importuje moduł jako `IJSObjectReference` , który reprezentuje odwołanie do obiektu JavaScript z kodu platformy .NET. Użyj `IJSObjectReference` do wywołania wyeksportowanych funkcji języka JavaScript z modułu:

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` reprezentuje odwołanie do obiektu JavaScript, którego funkcje mogą być wywoływane synchronicznie.

`IJSUnmarshalledObjectReference` reprezentuje odwołanie do obiektu JavaScript, którego funkcje mogą być wywoływane bez nakładu na Serializowanie danych platformy .NET. Może to być stosowane w Blazor WebAssembly przypadku, gdy wydajność jest istotna:

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

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

W programie Blazor WebAssembly platforma nie nakłada ograniczeń dotyczących rozmiaru danych wejściowych i wyjściowych wywołań js międzyoperacyjnych.

W programie Blazor Server wynik wywołania elementu js Interop jest ograniczony przez maksymalny rozmiar ładunku wymuszony przez SignalR ( <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> ), który domyślnie 32 KB. Aplikacje, które próbują odpowiedzieć na wywołanie elementu JS Interop z ładunkiem większym niż <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> Zgłoś błąd. Większy limit można skonfigurować przez modyfikację <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> . W poniższym przykładzie ustawiono maksymalny rozmiar komunikatu odbioru na 64 KB (64 * 1024 * 1024):

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

Zwiększenie SignalR limitu jest kosztem wymaganym przez użycie większej liczby zasobów serwera i ujawnia serwerowi zwiększone ryzyko ze strony złośliwego użytkownika. Ponadto odczytywanie dużej ilości zawartości w usłudze do pamięci jako ciągów lub tablic bajtowych może również spowodować, że alokacje działające nieprawidłowo z modułem wyrzucania elementów bezużytecznych, co skutkuje dodatkowymi karami za wydajność. Jedną z opcji odczytu dużych ładunków jest rozważenie wysłania zawartości w mniejszych fragmentach i przetworzenie ładunku jako <xref:System.IO.Stream> . Można go użyć podczas odczytywania dużych ładunków JSON lub w przypadku, gdy dane są dostępne w języku JavaScript jako nieprzetworzone bajty. Przykład demonstrujący wysyłanie dużych ładunków binarnych w programie Blazor Server , które używają technik podobnych do `InputFile` składnika, można znaleźć w [przykładowej aplikacji przesyłania danych binarnych](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).

Podczas opracowywania kodu, który przesyła dużą ilość danych między językami JavaScript i należy wziąć pod uwagę następujące wskazówki Blazor :

* Wydziel dane na mniejsze fragmenty i Wyślij segmenty danych sekwencyjnie, dopóki wszystkie dane nie zostaną odebrane przez serwer.
* Nie przydzielaj dużych obiektów w kodzie JavaScript i C#.
* Nie blokuj głównego wątku interfejsu użytkownika przez długie okresy podczas wysyłania lub otrzymywania danych.
* Zwolnij wszystkie używane pamięci, gdy proces zostanie ukończony lub anulowany.
* Wymuś następujące dodatkowe wymagania dotyczące zabezpieczeń:
  * Zadeklaruj maksymalny rozmiar pliku lub danych, który może zostać przesłany.
  * Zadeklaruj minimalną szybkość przekazywania z klienta na serwerze.
* Po odebraniu danych przez serwer dane mogą być następujące:
  * Tymczasowo przechowywane w buforze pamięci do momentu zebrania wszystkich segmentów.
  * Wykorzystano natychmiast. Na przykład dane mogą być przechowywane bezpośrednio w bazie danych lub zapisywane na dysku w miarę odbierania poszczególnych segmentów.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent. Razor — przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałąź wydania)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
