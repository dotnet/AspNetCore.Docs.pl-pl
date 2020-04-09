---
title: Wywoływanie metod platformy .NET z funkcji JavaScript w ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak wywoływać metody platformy Blazor .NET z funkcji JavaScript w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e2344dd15efd243a405373b6cf0362f28b48173a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976953"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>Wywoływanie metod platformy .NET z funkcji JavaScript w ASP.NET CoreBlazor

[Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplikacja Blazor może wywoływać funkcje JavaScript z metod .NET i .NET z funkcji JavaScript. Scenariusze te są nazywane *interoperacyjnością JavaScript* *(JS interop*).

W tym artykule omówiono wywoływanie metod platformy .NET z języka JavaScript. Aby uzyskać informacje na temat wywoływania funkcji <xref:blazor/call-javascript-from-dotnet>JavaScript z platformy .NET, zobacz .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Statyczne wywołanie metody .NET

Aby wywołać statyczną metodę .NET z `DotNet.invokeMethod` języka `DotNet.invokeMethodAsync` JavaScript, należy użyć funkcji lub. Przekaż w identyfikatorzie metody statycznej, którą chcesz wywołać, nazwę zestawu zawierającego funkcję i wszelkie argumenty. Wersja asynchroniowa jest preferowana do obsługi Blazor scenariuszy serwera. Metoda .NET musi być publiczna, statyczna i mieć `[JSInvokable]` atrybut. Wywoływanie otwartych metod ogólnych nie jest obecnie obsługiwane.

Przykładowa aplikacja zawiera metodę języka `int` C#, aby zwrócić tablicę. Atrybut `JSInvokable` jest stosowany do metody.

*Strony/JsInterop.brzytwa*:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

JavaScript obsługiwany klientowi wywołuje metodę C# .NET.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Po wybraniu przycisku **Trigger .NET metoda statyczna ReturnArrayAsync,** sprawdź dane wyjściowe konsoli w narzędziach programistycznych sieci web przeglądarki.

Wyjście konsoli jest następujące:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Czwarta wartość tablicy jest wypychany do tablicy (`data.push(4);`) zwracany przez `ReturnArrayAsync`.

Domyślnie identyfikator metody jest nazwą metody, ale można określić inny `JSInvokableAttribute` identyfikator przy użyciu konstruktora:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

W pliku JavaScript po stronie klienta:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>Wywołanie metody wystąpienia

Można również wywołać metody wystąpienia platformy .NET z języka JavaScript. Aby wywołać metodę wystąpienia platformy .NET z języka JavaScript:

* Przekaż wystąpienie .NET przez odwołanie do języka JavaScript:
  * Nawiązać statyczne `DotNetObjectReference.Create`wywołanie do pliku .
  * Zawiń wystąpienie `DotNetObjectReference` w `Create` wystąpieniu `DotNetObjectReference` i wywołaj wystąpienie. Zutylizuj `DotNetObjectReference` obiekty (przykład pojawia się w dalszej części tej sekcji).
* Wywołaj metody wystąpienia .NET w `invokeMethod` `invokeMethodAsync` wystąpieniu przy użyciu funkcji lub. Wystąpienie platformy .NET można również przekazać jako argument podczas wywoływania innych metod platformy .NET z języka JavaScript.

> [!NOTE]
> Przykładowa aplikacja rejestruje komunikaty do konsoli po stronie klienta. Poniższe przykłady zademonstrowane przez przykładową aplikację należy sprawdzić dane wyjściowe konsoli przeglądarki w narzędziach deweloperskich przeglądarki.

Gdy **trigger .NET metody wystąpienia HelloHelper.SayHello** przycisk jest zaznaczony, `ExampleJsInterop.CallHelloHelperSayHello` jest wywoływana i przekazuje nazwę, `Blazor`do metody.

*Strony/JsInterop.brzytwa*:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello`wywołuje funkcję `sayHello` JavaScript z nowym `HelloHelper`wystąpieniem .

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Nazwa jest przekazywana do `HelloHelper`konstruktora , który ustawia `HelloHelper.Name` właściwość. Po wykonaniu funkcji `sayHello` JavaScript `HelloHelper.SayHello` zwraca `Hello, {Name}!` komunikat, który jest zapisywany na konsoli przez funkcję JavaScript.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Dane wyjściowe konsoli w narzędziach programistycznych przeglądarki:

```console
Hello, Blazor!
```

Aby uniknąć przecieku pamięci i zezwolić na `DotNetObjectReference`wyrzucanie elementów bezużytecznych na składniku, który tworzy , przyjąć jedną z następujących metod:

* Zutylizuj obiekt w `DotNetObjectReference` klasie, która utworzyła wystąpienie:

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime _jsRuntime;
      private DotNetObjectReference<HelloHelper> _objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return _jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

  Poprzedni wzorzec pokazany `ExampleJsInterop` w klasie można również zaimplementować w komponencie:

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> _objRef;

      public async Task TriggerNetInstanceMethod()
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

* Jeśli składnik lub klasa nie pozbywa się `DotNetObjectReference`, zbywaj obiekt `.dispose()`na kliencie, wywołując:

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Wywołanie metody wystąpienia składnika

Aby wywołać metody .NET składnika:

* Użyj `invokeMethod` funkcji `invokeMethodAsync` lub, aby wywołać metodę statyczną do składnika.
* Metoda statyczna składnika zawija wywołanie metody wystąpienia `Action`jako wywoływane .

W języku JavaScript po stronie klienta:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Strony/JSInteropComponent.brzytwa*:

```razor
@page "/JSInteropComponent"

<p>
    Message: @_message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action _action;
    private string _message = "Select the button.";

    protected override void OnInitialized()
    {
        _action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

Gdy istnieje kilka składników, każdy z metod wystąpienia do wywołania, należy użyć `Action`klasy pomocnika do wywołania metody wystąpienia (jako s) każdego składnika.

W poniższym przykładzie:

* Składnik `JSInterop` zawiera `ListItem` kilka składników.
* Każdy `ListItem` składnik składa się z wiadomości i przycisku.
* Po `ListItem` wybraniu przycisku `ListItem`komponentu `UpdateMessage` metoda ta zmienia tekst elementu listy i ukrywa przycisk.

*MessageUpdateInvokeHelper.cs:*

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action _action;

    public MessageUpdateInvokeHelper(Action action)
    {
        _action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

W języku JavaScript po stronie klienta:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Shared/ListItem.brzytwa*:

```razor
@inject IJSRuntime JsRuntime

<li>
    @_message
    <button @onclick="InteropCall" style="display:@_display">InteropCall</button>
</li>

@code {
    private string _message = "Select one of these list item buttons.";
    private string _display = "inline-block";
    private MessageUpdateInvokeHelper _messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        _messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(_messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        _display = "none";
        StateHasChanged();
    }
}
```

*Strony/JSInterop.brzytwa*:

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Unikaj odwołań do obiektów cyklicznych

Obiekty, które zawierają odwołania cykliczne nie mogą być serializowane na kliencie dla jednego z:

* Wywołania metody .NET.
* Metoda JavaScript wywołuje z języka C#, gdy typ zwracany ma odwołania cykliczne.

Aby uzyskać więcej informacji, zobacz następujące problemy:

* [Odwołania cykliczne nie są obsługiwane, weź dwa (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Propozycja: Dodaj mechanizm do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/runtime)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:blazor/call-javascript-from-dotnet>
* [Przykład InteropComponent.razor (repozytorium Dotnet/AspNetCore GitHub, gałąź wydania 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Wykonywanie dużych transferów danych w Blazor aplikacjach serwera](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
