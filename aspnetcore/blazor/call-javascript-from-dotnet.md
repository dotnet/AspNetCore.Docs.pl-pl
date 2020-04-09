---
title: Wywoływanie funkcji JavaScript z metod .NET w ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak wywoływać funkcje Blazor JavaScript z metod platformy .NET w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 0c6b6a0a8f88fa912523e7772fcd84ef4ce3b4ff
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977018"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a>Wywoływanie funkcji JavaScript z metod .NET w ASP.NET CoreBlazor

[Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27)i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplikacja Blazor może wywoływać funkcje JavaScript z metod .NET i .NET z funkcji JavaScript. Scenariusze te są nazywane *interoperacyjnością JavaScript* *(JS interop*).

W tym artykule omówiono wywoływanie funkcji JavaScript z platformy .NET. Aby uzyskać informacje na temat wywoływania metod <xref:blazor/call-dotnet-from-javascript>platformy .NET z języka JavaScript, zobacz .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Aby wywołać javascript z platformy `IJSRuntime` .NET, użyj abstrakcji. Aby wystawić wywołania interop JS, wstrzyknąć `IJSRuntime` abstrakcję w składniku. Metoda `InvokeAsync<T>` przyjmuje identyfikator funkcji JavaScript, który chcesz wywołać wraz z dowolną liczbą argumentów serializable JSON. Identyfikator funkcji jest względem zakresu globalnego`window`( ). Jeśli chcesz zadzwonić, `window.someScope.someFunction`identyfikator `someScope.someFunction`jest . Nie ma potrzeby rejestrowania funkcji, zanim ją wywoła. Zwracany `T` typ musi być również serializable JSON. `T`powinien być zgodny z typem .NET, który najlepiej mapuje zwracany typ JSON.

W Blazor przypadku aplikacji serwera z włączoną funkcją wstępnego wywoływania w języku JavaScript nie jest możliwe podczas wstępnego wstępnego wstępnego wstępnego wstępnego rozsyłania. Połączenia interop JavaScript muszą zostać odroczone do czasu nawiązania połączenia z przeglądarką. Aby uzyskać więcej informacji, zobacz [wykrywanie, Blazor gdy aplikacja serwer jest prerendering](#detect-when-a-blazor-server-app-is-prerendering) sekcji.

Poniższy przykład jest oparty na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), dekoder oparty na JavaScript. W przykładzie pokazano, jak wywołać funkcję JavaScript z metody C#. Funkcja JavaScript akceptuje tablicę bajtów z metody C#, dekoduje tablicę i zwraca tekst do składnika do wyświetlenia.

Wewnątrz `<head>` elementu *wwwroot/index.html* (WebAssembly)Blazor lub *Pages/_Host.cshtml* (Serwer)Blazor podaj funkcję JavaScript, która służy `TextDecoder` do dekodowania przekazanej tablicy i zwraca wartość zdekodowaną:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Kod JavaScript, taki jak kod pokazany w poprzednim przykładzie, można również załadować z pliku JavaScript (*.js*) z odwołaniem do pliku skryptu:

```html
<script src="exampleJsInterop.js"></script>
```

Następujący składnik:

* Wywołuje funkcję `convertArray` JavaScript `JSRuntime` przy użyciu po wybraniu przycisku składnika **(Konwertuj tablicę).**
* Po wywołaniu funkcji JavaScript przekazana tablica jest konwertowana na ciąg. Ciąg jest zwracany do składnika do wyświetlania.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>Czas IJSRuntime

Aby użyć `IJSRuntime` abstrakcji, należy przyjąć dowolną z następujących metod:

* Wstrzyknąć `IJSRuntime` abstrakcję do komponentu Razor (*.brzytwa):*

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Wewnątrz `<head>` elementu *wwwroot/index.html* (WebAssembly)Blazor lub *Pages/_Host.cshtml* (Serwer)Blazor podaj `handleTickerChanged` funkcję JavaScript. Funkcja jest wywoływana z `IJSRuntime.InvokeVoidAsync` i nie zwraca wartości:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Wstrzyknąć `IJSRuntime` abstrakcję do klasy (*.cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Wewnątrz `<head>` elementu *wwwroot/index.html* (WebAssembly)Blazor lub *Pages/_Host.cshtml* (Serwer)Blazor podaj `handleTickerChanged` funkcję JavaScript. Funkcja jest wywoływana z `JSRuntime.InvokeAsync` i zwraca wartość:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* W przypadku generowania zawartości dynamicznej `[Inject]` za pomocą [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)użyj atrybutu:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

W przykładowej aplikacji po stronie klienta, która towarzyszy w tym temacie, dwie funkcje JavaScript są dostępne dla aplikacji, które współdziałają z dom do odbierania danych wejściowych użytkownika i wyświetlania wiadomości powitalnej:

* `showPrompt`&ndash; Generuje monit o zaakceptowanie danych wejściowych użytkownika (nazwa użytkownika) i zwraca nazwę do osoby dzwoniącej.
* `displayWelcome`&ndash; Przypisuje wiadomość powitalną od osoby dzwoniącej do `id` `welcome`obiektu DOM z elementem .

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Umieść `<script>` znacznik, który odwołuje się do *wwwroot/index.html* pliku JavaScriptBlazor w pliku wwwroot/index.html (WebAssembly) lub *Pages/_Host.cshtml* Blazor (Serwer).

*wwwroot/index.html* (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Strony/_Host.cshtml* (Blazor Serwer):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Nie umieszczaj `<script>` znacznika w pliku `<script>` składnika, ponieważ tag nie może być aktualizowany dynamicznie.

.NET metody współdziałania z funkcjami JavaScript w *pliku exampleJsInterop.js* przez wywołanie `IJSRuntime.InvokeAsync<T>`.

Abstrakcja `IJSRuntime` jest asynchroniza, Blazor aby umożliwić scenariusze serwera. Jeśli aplikacja jest Blazor aplikacją WebAssembly i chcesz wywołać funkcję JavaScript synchronicznie, zamiast tego można go `IJSInProcessRuntime` przesunąć w dół i wywołać. `Invoke<T>` Zaleca się, że większość bibliotek interop JS używać asynchronizycznych interfejsów API, aby upewnić się, że biblioteki są dostępne we wszystkich scenariuszach.

Przykładowa aplikacja zawiera składnik, aby zademonstrować js interop. Składnik:

* Odbiera dane wejściowe użytkownika za pomocą monitu JavaScript.
* Zwraca tekst do składnika do przetwarzania.
* Wywołuje drugą funkcję JavaScript, która współdziała z dom, aby wyświetlić wiadomość powitalną.

*Strony/JSInterop.brzytwa*:

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

1. Po `TriggerJsPrompt` wykonaniu przez wybranie składnika **Trigger JavaScript Prompt** `showPrompt` przycisk, funkcja JavaScript pod warunkiem, że w *pliku wwwroot/exampleJsInterop.js* jest wywoływana.
1. Funkcja `showPrompt` akceptuje dane wejściowe użytkownika (nazwa użytkownika), który jest zakodowany w formacie HTML i zwracany do składnika. Składnik przechowuje nazwę użytkownika w zmiennej `name`lokalnej, .
1. Ciąg przechowywany `name` w jest włączony do wiadomości powitalnej, która jest `displayWelcome`przekazywana do funkcji JavaScript, która renderuje wiadomość powitalną do tagu nagłówka.

## <a name="call-a-void-javascript-function"></a>Wywoływanie funkcji JavaScript unieważnienia

Funkcje JavaScript, które zwracają [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) `IJSRuntime.InvokeVoidAsync`lub [undefined,](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) są wywoływane z .

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a>Wykrywanie, Blazor kiedy aplikacja serwera jest prerendering
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Przechwytywanie odniesień do elementów

Niektóre scenariusze interop JS wymagają odwołań do elementów HTML. Na przykład biblioteka interfejsu użytkownika może wymagać odwołania do elementu do inicjowania lub może być `focus` `play`konieczne wywołanie interfejsów API podobnych do polecenia na elemencie, takim jak lub .

Przechwytywanie odwołań do elementów HTML w składniku przy użyciu następującego podejścia:

* Dodaj `@ref` atrybut do elementu HTML.
* Zdefiniuj `ElementReference` pole typu, którego `@ref` nazwa odpowiada wartości atrybutu.

Poniższy przykład pokazuje przechwytywanie `username` `<input>` odwołania do elementu:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Użyj tylko odwołania do elementu, aby zmutować zawartość pustego elementu, który nie wchodzi w interakcję z Blazorprogramem . Ten scenariusz jest przydatny, gdy interfejs API innej firmy dostarcza zawartość do elementu. Ponieważ Blazor nie wchodzi w interakcję z elementem, nie ma Blazormożliwości konfliktu między reprezentacji elementu i DOM.
>
> W poniższym przykładzie *niebezpieczne* jest mutowanie zawartości listy nieuporządkowanej (`ul`), ponieważ Blazor współdziała z dom,`<li>`aby wypełnić elementy listy tego elementu ( ):
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
> Jeśli JS interop mutuje `MyList` zawartość Blazor elementu i próbuje zastosować różnice do elementu, różnice nie będą zgodne z DOM.

Jeśli chodzi o kod .NET, `ElementReference` jest nieprzezroczystym uchwytem. *Jedyną* rzeczą, którą `ElementReference` możesz zrobić, to przekazać go do kodu JavaScript za pośrednictwem JS interop. Po wykonaniu tej tej pracy kod po `HTMLElement` stronie języka JavaScript odbiera wystąpienie, którego może używać z normalnymi interfejsami API DOM.

Na przykład następujący kod definiuje metodę rozszerzenia .NET, która umożliwia ustawienie fokusu na elemencie:

*przykładJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Aby wywołać funkcję JavaScript, która nie `IJSRuntime.InvokeVoidAsync`zwraca wartości, użyj . Poniższy kod ustawia fokus na wejściu nazwy użytkownika, `ElementReference`wywołując poprzednią funkcję JavaScript z przechwyconym:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Aby użyć metody rozszerzenia, należy utworzyć metodę `IJSRuntime` rozszerzenia statycznego, która odbiera wystąpienie:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Metoda `Focus` jest wywoływana bezpośrednio na obiekcie. W poniższym `Focus` przykładzie przyjęto założenie, że metoda jest dostępna z `JsInteropClasses` obszaru nazw:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> Zmienna `username` jest wypełniana tylko po renderowaniu składnika. Jeśli niezaludniony `ElementReference` kod javascript zostanie przekazany do kodu `null`JavaScript, kod JavaScript otrzymuje wartość . Aby manipulować odwołaniami do elementów po zakończeniu renderowania przez komponent (aby ustawić początkowy fokus na elemencie), należy użyć [metod cyklu życia komponentu OnAfterRenderAsync lub OnAfterRender](xref:blazor/lifecycle#after-component-render).

Podczas pracy z typami ogólnymi i zwracania wartości należy użyć [valuetask\<T>: ](xref:System.Threading.Tasks.ValueTask`1)

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`jest wywoływana bezpośrednio na obiekcie z typem. W poniższym `GenericMethod` przykładzie przyjęto `JsInteropClasses` założenie, że jest dostępna z obszaru nazw:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Elementy referencyjne między komponentami

Jest `ElementReference` gwarantowana tylko prawidłowe `OnAfterRender` w metodzie składnika `struct`(i odwołanie do elementu jest ), więc odwołanie do elementu nie mogą być przekazywane między składnikami.

Aby składnik nadrzędny udostępnił odniesienie do elementu innym komponentom, składnik nadrzędny może:

* Zezwalaj składnikom podrzędnym na rejestrowanie wywołań zwrotnych.
* Wywołać zarejestrowanych wywołań `OnAfterRender` zwrotnych podczas zdarzenia z odwołaniem do elementu przekazywane. Pośrednio takie podejście umożliwia składników podrzędnych do interakcji z odwołaniem elementu nadrzędnego.

Poniższy Blazor przykład WebAssembly ilustruje podejście.

W `<head>` *wwwroot/index.html:*

```html
<style>
    .red { color: red }
</style>
```

W `<body>` *wwwroot/index.html:*

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/Index.brzytwa* (składnik nadrzędny):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Strony/Index.razor.cs*:

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

*Shared/SurveyPrompt.brzytwa* (składnik podrzędny):

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

*Wspólne/SurveyPrompt.razor.cs*:

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

## <a name="harden-js-interop-calls"></a>Harden JS połączeń interop

JS interop może zakończyć się niepowodzeniem z powodu błędów sieciowych i powinny być traktowane jako zawodne. Domyślnie Blazor aplikacja serwera upotrzyma js interop wywołania na serwerze po jednej minucie. Jeśli aplikacja może tolerować bardziej agresywny limit czasu, na przykład 10 sekund, ustaw limit czasu przy użyciu jednego z następujących metod:

* Globalnie `Startup.ConfigureServices`w , określ limit czasu:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Na wywołanie w kodzie składnika pojedyncze wywołanie może określić limit czasu:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Aby uzyskać więcej informacji na <xref:security/blazor/server>temat wyczerpania zasobów, zobacz .

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Unikaj odwołań do obiektów cyklicznych

Obiekty, które zawierają odwołania cykliczne nie mogą być serializowane na kliencie dla jednego z:

* Wywołania metody .NET.
* Metoda JavaScript wywołuje z języka C#, gdy typ zwracany ma odwołania cykliczne.

Aby uzyskać więcej informacji, zobacz następujące problemy:

* [Odwołania cykliczne nie są obsługiwane, weź dwa (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Propozycja: Dodaj mechanizm do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/runtime)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:blazor/call-dotnet-from-javascript>
* [Przykład InteropComponent.razor (repozytorium Dotnet/AspNetCore GitHub, gałąź wydania 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Wykonywanie dużych transferów danych w Blazor aplikacjach serwera](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
