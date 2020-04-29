---
title: Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak wywoływać funkcje języka JavaScript Blazor z metod .NET w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 380a14177d4bb8fa3de63a3c1cd9a39aeab13db3
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205985"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a>Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET CoreBlazor

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)i [Luke](https://github.com/guardrex) Latham

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Aplikacja może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript. Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie*js Interop*).

W tym artykule opisano wywoływanie funkcji języka JavaScript z platformy .NET. Aby uzyskać informacje na temat wywoływania metod .NET w języku JavaScript, <xref:blazor/call-dotnet-from-javascript>Zobacz.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Aby wywołać kod JavaScript z platformy .NET, użyj `IJSRuntime` abstrakcji. Aby wystawić wywołania programu JS Interop `IJSRuntime` , wstrzyknąć streszczenie w składniku. `InvokeAsync<T>` Metoda przyjmuje identyfikator dla funkcji języka JavaScript, która ma zostać wywołana wraz z dowolną liczbą argumentów do serializacji JSON. Identyfikator funkcji jest względny w stosunku do zakresu globalnego`window`(). Jeśli chcesz wywołać `window.someScope.someFunction`, identyfikator to `someScope.someFunction`. Nie ma potrzeby rejestrowania funkcji przed jej wywołaniem. Zwracanym typem `T` musi być również kod JSON możliwy do serializacji. `T`powinien być zgodny z typem .NET, który najlepiej jest mapowany do zwracanego typu JSON.

W Blazor przypadku aplikacji serwerowych z włączoną funkcją prerenderowania Wywoływanie kodu JavaScript nie jest możliwe podczas początkowego wstępnego renderowania. Wywołania międzyoperacyjne języka JavaScript muszą zostać odroczone do momentu ustanowienia połączenia z przeglądarką. Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, Blazor kiedy aplikacja serwerowa jest renderowana](#detect-when-a-blazor-server-app-is-prerendering) .

Poniższy przykład jest oparty na [dekoderze](https://developer.mozilla.org/docs/Web/API/TextDecoder), dekoder języka JavaScript. W przykładzie pokazano, jak wywołać funkcję JavaScript z metody języka C#. Funkcja JavaScript akceptuje tablicę bajtową z metody C#, dekoduje tablicę i zwraca tekst do składnika do wyświetlenia.

`<head>` Wewnątrz elementu *wwwroot/index.html* (Blazor webassembly) lub *Pages/_Host. cshtml* (Blazor serwer), podaj funkcję języka JavaScript, która używa `TextDecoder` do dekodowania przekazaną tablicę i zwracają zdekodowaną wartość:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Kod JavaScript, taki jak kod przedstawiony w powyższym przykładzie, można również załadować z pliku JavaScript (*. js*) z odwołaniem do pliku skryptu:

```html
<script src="exampleJsInterop.js"></script>
```

Następujący składnik:

* Wywołuje funkcję `convertArray` JavaScript przy użyciu `JSRuntime` przycisku składnika (**Konwertuj tablicę**).
* Po wywołaniu funkcji języka JavaScript przenoszona tablica jest konwertowana na ciąg. Ciąg jest zwracany do składnika do wyświetlenia.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Aby użyć `IJSRuntime` abstrakcji, należy zastosować jedną z następujących metod:

* Wstrzyknąć `IJSRuntime` abstrakcję do składnika Razor (*. Razor*):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  W `<head>` elemencie elementu *wwwroot/index.html* (Blazor webassembly) lub *Pages/_Host. cshtml* (Blazor serwer) podaj funkcję `handleTickerChanged` języka JavaScript. Funkcja jest wywoływana z `IJSRuntime.InvokeVoidAsync` i nie zwraca wartości:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Wstrzyknąć `IJSRuntime` streszczenie do klasy (*. cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  W `<head>` elemencie elementu *wwwroot/index.html* (Blazor webassembly) lub *Pages/_Host. cshtml* (Blazor serwer) podaj funkcję `handleTickerChanged` języka JavaScript. Funkcja jest wywoływana z `JSRuntime.InvokeAsync` i zwraca wartość:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* W przypadku generowania zawartości dynamicznej przy użyciu [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)należy `[Inject]` użyć atrybutu:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

W aplikacji przykładowej po stronie klienta, która jest dołączona do tego tematu, dostępne są dwie funkcje języka JavaScript, które współdziałają z modelem DOM, aby odbierać dane wejściowe użytkownika i wyświetlać komunikat powitalny:

* `showPrompt`&ndash; Generuje monit o zaakceptowanie danych wprowadzonych przez użytkownika (nazwę użytkownika) i zwraca nazwę obiektu wywołującego.
* `displayWelcome`&ndash; Przypisuje Komunikat powitalny od wywołującego do obiektu Dom z `id` `welcome`.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

`<script>` Umieść tag odwołujący się do pliku JavaScript w pliku *wwwroot/index.html* (Blazor webassembly) lub *Pages/_Host. cshtml* (Blazor serwer).

*wwwroot/index.html* (Blazor webassembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host. cshtml* (Blazor serwer):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Nie umieszczaj `<script>` znacznika w pliku składnika, `<script>` ponieważ nie można dynamicznie zaktualizować znacznika.

.NET metod współdziałania z funkcjami JavaScript w pliku *exampleJsInterop. js* przez wywołanie `IJSRuntime.InvokeAsync<T>`.

`IJSRuntime` Abstrakcja jest asynchroniczna, aby umożliwić Blazor obsługę scenariuszy serwera. Jeśli aplikacja jest Blazor aplikacją webassembly i chcesz wywołać funkcję JavaScript synchronicznie, downcast do `IJSInProcessRuntime` i Wywołaj `Invoke<T>` zamiast tego. Zalecamy, aby większość bibliotek międzyoperacyjnych JS używała asynchronicznych interfejsów API, aby upewnić się, że biblioteki są dostępne we wszystkich scenariuszach.

Przykładowa aplikacja zawiera składnik demonstrujący międzyoperacyjność JS. Składnik:

* Odbiera dane wprowadzane przez użytkownika za pośrednictwem wiersza polecenia języka JavaScript.
* Zwraca tekst do składnika do przetworzenia.
* Wywołuje drugą funkcję języka JavaScript, która współdziała z modelem DOM, aby wyświetlić komunikat powitalny.

*Strony/JSInterop. Razor*:

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
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

1. Gdy `TriggerJsPrompt` jest wykonywane, zaznaczając przycisk **Monituj wyzwalacza JavaScript** składnika, funkcja języka `showPrompt` JavaScript dostępna w pliku *wwwroot/exampleJsInterop. js* jest wywoływana.
1. `showPrompt` Funkcja akceptuje dane wejściowe użytkownika (nazwę użytkownika), które są kodowane w formacie HTML i zwracane do składnika. Składnik przechowuje nazwę użytkownika w zmiennej lokalnej, `name`.
1. Ciąg przechowywany w programie `name` jest zawarty w komunikacie powitalnym, który jest przesyłany do funkcji `displayWelcome`języka JavaScript, która renderuje Komunikat powitalny do znacznika nagłówka.

## <a name="call-a-void-javascript-function"></a>Wywoływanie funkcji języka JavaScript typu void

Funkcje języka JavaScript zwracające [wartość void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) lub [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) są wywoływane z `IJSRuntime.InvokeVoidAsync`.

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a>Wykrywaj, Blazor kiedy aplikacja serwerowa jest renderowana
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Przechwyć odwołania do elementów

Niektóre scenariusze międzyoperacyjności JS wymagają odwołań do elementów HTML. Na przykład Biblioteka interfejsu użytkownika może wymagać odwołania do elementu dla inicjalizacji lub może być konieczne wywołanie interfejsów API, takich jak `focus` lub. `play`

Przechwyć odwołania do elementów HTML w składniku, korzystając z następującej metody:

* Dodaj `@ref` atrybut do elementu HTML.
* Zdefiniuj pole typu `ElementReference` , którego nazwa pasuje do wartości `@ref` atrybutu.

Poniższy przykład pokazuje przechwytywanie odwołania do `username` `<input>` elementu:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Użyj odwołania do elementu, aby zmodyfikować zawartość pustego elementu, który nie współdziała z Blazor. Ten scenariusz jest przydatny, gdy interfejs API innej firmy dostarcza zawartość do elementu. Ponieważ Blazor nie współdziała z elementem, nie ma możliwości konfliktu między Blazorreprezentacją elementu a modelem dom.
>
> W poniższym przykładzie jest *niebezpieczne* do mutacji zawartości listy nieuporządkowanej (`ul`), ponieważ Blazor współdziała z modelem dom w celu wypełnienia elementów listy elementu (`<li>`):
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

W odniesieniu do kodu platformy .NET jest `ElementReference` to nieprzezroczyste dojście. *Jedyną* czynnością, którą można wykonać `ElementReference` , jest przekazanie jej do kodu JavaScript za pośrednictwem międzyoperacyjnego js. Gdy to zrobisz, kod po stronie JavaScript odbiera `HTMLElement` wystąpienie, które może być używane z normalnymi interfejsami API modelu DOM.

Na przykład poniższy kod definiuje metodę rozszerzenia .NET, która umożliwia ustawienie fokusu na elemencie:

*exampleJsInterop. js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Aby wywołać funkcję języka JavaScript, która nie zwraca wartości, użyj `IJSRuntime.InvokeVoidAsync`. Poniższy kod ustawia fokus na wejściu do nazwy użytkownika, wywołując poprzednią funkcję JavaScript z przechwyconą `ElementReference`:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Aby użyć metody rozszerzenia, Utwórz statyczną metodę rozszerzenia, która odbiera `IJSRuntime` wystąpienie:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus` Metoda jest wywoływana bezpośrednio dla obiektu. W poniższym przykładzie przyjęto założenie, że `Focus` Metoda jest `JsInteropClasses` dostępna z przestrzeni nazw:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> `username` Zmienna jest wypełniana tylko po wyrenderowaniu składnika. W przypadku przekazanie niewypełnionego `ElementReference` kodu JavaScript kod JavaScript otrzymuje wartość. `null` Aby manipulować odwołaniami do elementów po zakończeniu renderowania składnika (aby ustawić początkowy fokus w elemencie), użyj [metod cyklu życia składnika OnAfterRenderAsync lub OnAfterRender](xref:blazor/lifecycle#after-component-render).

Podczas pracy z typami ogólnymi i zwracania wartości należy użyć [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`jest wywoływana bezpośrednio na obiekcie z typem. W poniższym przykładzie przyjęto założenie, że `GenericMethod` jest `JsInteropClasses` dostępny z przestrzeni nazw:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Elementy odniesienia między składnikami

Element `ElementReference` jest gwarantowany tylko w `OnAfterRender` metodzie składnika (i odwołania do elementu `struct`), dlatego nie można przekazywać odwołania do elementu między składnikami.

Aby składnik nadrzędny mógł udostępnić odwołanie do elementu innym składnikom, składnik nadrzędny może:

* Zezwalaj składnikom podrzędnym na rejestrowanie wywołań zwrotnych.
* Wywołaj zarejestrowane wywołania zwrotne podczas `OnAfterRender` zdarzenia z odwołaniem do elementu. Pośrednio takie podejście umożliwia składnikom podrzędnym współdziałanie z odwołaniem do elementu nadrzędnego.

Poniższy Blazor przykład zestawu webassembly ilustruje podejście.

`<head>` W *wwwroot/index.html*:

```html
<style>
    .red { color: red }
</style>
```

`<body>` W *wwwroot/index.html*:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/index. Razor* (składnik nadrzędny):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Pages/index. Razor. cs*:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

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
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Shared/SurveyPrompt. Razor* (składnik podrzędny):

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

*Shared/SurveyPrompt. Razor. cs*:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a>Zabezpieczenia wywołań międzyoperacyjnych w ramach funkcjonalności JS

Usługa JS Interop może zakończyć się niepowodzeniem z powodu błędów sieci i powinna być traktowana jako niezawodna. Domyślnie aplikacja Blazor serwera przeprowadzi czas wywołań międzyoperacyjnych js na serwerze po jednej minucie. Jeśli aplikacja może tolerować bardziej agresywny limit czasu, na przykład 10 sekund, należy ustawić limit czasu przy użyciu jednej z następujących metod:

* Globalnie w `Startup.ConfigureServices`programie Określ limit czasu:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Dla wywołania w kodzie składnika pojedyncze wywołanie może określać limit czasu:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Więcej informacji o wyczerpaniu zasobów znajduje się w <xref:security/blazor/server/threat-mitigation>temacie.

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Unikaj cyklicznych odwołań do obiektów

Obiekty, które zawierają odwołania cykliczne, nie mogą być serializowane na kliencie dla obu:

* Wywołania metody .NET.
* Wywołania metody JavaScript z języka C#, gdy typem zwracanym są odwołania cykliczne.

Aby uzyskać więcej informacji, zobacz następujące problemy:

* [Odwołania cykliczne nie są obsługiwane, zrób dwa (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Propozycja: Dodawanie mechanizmu do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent. Razor — przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałąź wydania)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Wykonywanie dużych transferów danych Blazor w aplikacjach serwera](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
