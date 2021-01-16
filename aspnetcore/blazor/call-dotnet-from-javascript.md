---
title: Wywoływanie metod .NET z funkcji języka JavaScript w ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak wywoływać metody .NET z funkcji języka JavaScript w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 5a00bfb87b8cfe0fb3e2a832a553b8a4cd45ee6d
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252503"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a>Wywoływanie metod .NET z funkcji języka JavaScript w ASP.NET Core Blazor

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)i [Luke](https://github.com/guardrex) Latham

BlazorAplikacja może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript. Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie *js Interop*).

W tym artykule opisano wywoływanie metod .NET z języka JavaScript. Aby uzyskać informacje na temat wywoływania funkcji JavaScript z platformy .NET, zobacz <xref:blazor/call-javascript-from-dotnet> .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Statyczne wywołanie metody .NET

Aby wywołać statyczną metodę .NET z poziomu języka JavaScript, `DotNet.invokeMethod` Użyj `DotNet.invokeMethodAsync` funkcji or. Przekaż identyfikator metody statycznej, która ma być wywoływana, nazwę zestawu zawierającego funkcję i wszelkie argumenty. Wersja asynchroniczna jest preferowana do obsługi Blazor Server scenariuszy. Metoda .NET musi być publiczna, statyczna i mieć [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atrybut. Wywoływanie otwartych metod ogólnych nie jest obecnie obsługiwane.

Przykładowa aplikacja zawiera metodę języka C#, która zwraca `int` tablicę. Ten [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atrybut jest stosowany do metody.

`Pages/JsInterop.razor`:

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

Kod JavaScript obsługiwany przez klienta wywołuje metodę języka C# .NET.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Po **`Trigger .NET static method ReturnArrayAsync`** wybraniu przycisku Sprawdź dane wyjściowe konsoli w narzędziach deweloperskich sieci Web w przeglądarce.

Dane wyjściowe konsoli są następujące:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Czwarta wartość tablicy jest wypychana do tablicy ( `data.push(4);` ) zwracanej przez `ReturnArrayAsync` .

Domyślnie identyfikator metody jest nazwą metody, ale można określić inny identyfikator przy użyciu [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) konstruktora atrybutu:

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
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).

## <a name="instance-method-call"></a>Wywołanie metody wystąpienia

Można również wywołać metody wystąpienia platformy .NET z poziomu języka JavaScript. Aby wywołać metodę wystąpienia platformy .NET z poziomu języka JavaScript:

* Przekaż wystąpienie platformy .NET przez odwołanie do języka JavaScript:
  * Wykonaj wywołanie statyczne do <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .
  * Zawiń wystąpienie w <xref:Microsoft.JSInterop.DotNetObjectReference> wystąpieniu i Wywołaj wystąpienie <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> <xref:Microsoft.JSInterop.DotNetObjectReference> . Usuwanie <xref:Microsoft.JSInterop.DotNetObjectReference> obiektów (przykład pojawia się w dalszej części tej sekcji).
* Wywołaj metody wystąpienia platformy .NET w wystąpieniu przy `invokeMethod` użyciu `invokeMethodAsync` funkcji lub. Wystąpienie programu .NET może być również przekazywać jako argument podczas wywoływania innych metod .NET z JavaScript.

> [!NOTE]
> Przykładowa aplikacja rejestruje komunikaty do konsoli po stronie klienta. W poniższych przykładach zademonstrowanych przez przykładową aplikację można sprawdzić dane wyjściowe konsoli przeglądarki w narzędziach deweloperskich w przeglądarce.

Gdy **`Trigger .NET instance method HelloHelper.SayHello`** przycisk jest zaznaczony, `ExampleJsInterop.CallHelloHelperSayHello` jest wywoływana i przekazuje nazwę, `Blazor` do metody.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello` wywołuje funkcję JavaScript `sayHello` z nowym wystąpieniem `HelloHelper` .

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Nazwa jest przenoszona do `HelloHelper` konstruktora, który ustawia `HelloHelper.Name` Właściwość. Gdy funkcja JavaScript `sayHello` jest wykonywana, `HelloHelper.SayHello` zwraca `Hello, {Name}!` komunikat, który jest zapisywana w konsoli przez funkcję JavaScript.

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Dane wyjściowe konsoli w narzędziach deweloperskich sieci Web w przeglądarce:

```console
Hello, Blazor!
```

Aby uniknąć przecieków pamięci i zezwolić na wyrzucanie elementów bezużytecznych w składniku, który tworzy <xref:Microsoft.JSInterop.DotNetObjectReference> , należy zastosować jedną z następujących metod:

* Usuń obiekt z klasy, która utworzyła <xref:Microsoft.JSInterop.DotNetObjectReference> wystąpienie:

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  Poprzedni wzorzec przedstawiony w `ExampleJsInterop` klasie może być również zaimplementowany w składniku:

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).

* Gdy składnik lub Klasa nie usuwa <xref:Microsoft.JSInterop.DotNetObjectReference> metody Dispose z klienta przez wywołanie `.dispose()` :

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Wywołanie metody wystąpienia składnika

Aby wywołać metody .NET składnika:

* Użyj `invokeMethod` funkcji or, `invokeMethodAsync` Aby wykonać wywołanie metody statycznej do składnika.
* Metoda statyczna składnika zawija wywołanie do metody instancji jako wywołane <xref:System.Action> .

> [!NOTE]
> W przypadku Blazor Server aplikacji, w których kilku użytkowników może jednocześnie korzystać z tego samego składnika, użyj klasy pomocnika, aby wywołać metody instancji.
>
> Aby uzyskać więcej informacji, zobacz sekcję [Klasa pomocnika metody wystąpienia składnika](#component-instance-method-helper-class) .

W języku JavaScript po stronie klienta:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).

`Pages/JSInteropComponent.razor`:

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

Aby przekazać argumenty do metody wystąpienia:

* Dodaj parametry do wywołania metody JS. W poniższym przykładzie nazwa jest przenoszona do metody. W razie potrzeby można dodać do listy dodatkowe parametry.

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).

* Podaj prawidłowe typy <xref:System.Action> dla parametrów. Podaj listę parametrów w metodach języka C#. Wywołaj <xref:System.Action> ( `UpdateMessage` ) z parametrami ( `action.Invoke(name)` ).

  `Pages/JSInteropComponent.razor`:

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  Wyjście `message` , gdy zostanie wybrany przycisk **metody wywołania js** :

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>Klasa pomocnika metody wystąpienia składnika

Klasa pomocnika służy do wywoływania metody wystąpienia jako <xref:System.Action> . Klasy pomocnika są przydatne w przypadku:

* Niektóre składniki tego samego typu są renderowane na tej samej stronie.
* Blazor ServerUżywana jest aplikacja, w której wielu użytkowników może używać składnika współbieżnie.

W poniższym przykładzie:

* `JSInteropExample`Składnik zawiera kilka `ListItem` składników.
* Każdy `ListItem` składnik składa się z komunikatu i przycisku.
* Po `ListItem` wybraniu przycisku składnik `ListItem` `UpdateMessage` Metoda zmienia tekst elementu listy i ukrywa przycisk.

`MessageUpdateInvokeHelper.cs`:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).

W języku JavaScript po stronie klienta:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).

`Shared/ListItem.razor`:

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

`Pages/JSInteropExample.razor`:

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Unikaj cyklicznych odwołań do obiektów

Obiekty, które zawierają odwołania cykliczne, nie mogą być serializowane na kliencie dla obu:

* Wywołania metody .NET.
* Wywołania metody JavaScript z języka C#, gdy typem zwracanym są odwołania cykliczne.

Aby uzyskać więcej informacji, zobacz następujące problemy:

* [Odwołania cykliczne nie są obsługiwane, zrób dwa (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Propozycja: Dodawanie mechanizmu do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a>Limity rozmiaru dla wywołań międzyoperacyjnych w JS

W programie Blazor WebAssembly platforma nie nakłada limitu rozmiaru danych wejściowych i wyjściowych w programie js.

W programie Blazor Server wywołania programu js Interop są ograniczone przez maksymalny SignalR rozmiar przychodzących komunikatów dozwolony dla metod koncentratora, które są wymuszane przez <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (domyślnie: 32 KB). Program JS do SignalR komunikatów programu .NET większych niż <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> Zgłoś błąd. Platforma nie nakłada limitu rozmiaru SignalR komunikatu z centrum na klienta.

Gdy SignalR Rejestrowanie nie jest ustawione na [debugowanie](xref:Microsoft.Extensions.Logging.LogLevel) lub [śledzenie](xref:Microsoft.Extensions.Logging.LogLevel), Błąd rozmiaru komunikatu pojawia się tylko w konsoli narzędzia deweloperskie w przeglądarce:

> Błąd: połączenie zostało rozłączone z błędem "błąd: serwer zwrócił błąd podczas zamykania: połączenie zostało zamknięte z błędem".

Gdy [ SignalR Rejestrowanie po stronie serwera](xref:signalr/diagnostics#server-side-logging) jest ustawione na [debugowanie](xref:Microsoft.Extensions.Logging.LogLevel) lub [śledzenie](xref:Microsoft.Extensions.Logging.LogLevel), podczas rejestrowania po stronie serwera jest wyświetlany <xref:System.IO.InvalidDataException> komunikat o błędzie.

`appsettings.Development.json`:

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> System. IO. InvalidDataException —: przekroczono maksymalny rozmiar komunikatu 32768B. Rozmiar komunikatu można skonfigurować w AddHubOptions.

Zwiększ limit przez ustawienie <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> w `Startup.ConfigureServices` . W poniższym przykładzie ustawiono maksymalny rozmiar komunikatu odbioru na 64 KB (64 * 1024):

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

Zwiększenie SignalR limitu rozmiaru komunikatów przychodzących jest kosztem wymagania większej liczby zasobów serwera i ujawnia serwerowi zwiększone ryzyko ze strony złośliwego użytkownika. Ponadto odczytywanie dużej ilości zawartości w usłudze do pamięci jako ciągów lub tablic bajtowych może również spowodować, że alokacje działające nieprawidłowo z modułem wyrzucania elementów bezużytecznych, co skutkuje dodatkowymi karami za wydajność.

Jedną z opcji odczytu dużych ładunków jest wysłanie zawartości w mniejszych fragmentach i przetworzenie ładunku jako <xref:System.IO.Stream> . Można go użyć podczas odczytywania dużych ładunków JSON lub w przypadku, gdy dane są dostępne w języku JavaScript jako nieprzetworzone bajty. Przykład demonstrujący wysyłanie dużych ładunków binarnych w programie Blazor Server , które używają technik podobnych do `InputFile` składnika, można znaleźć w [przykładowej aplikacji przesyłania danych binarnych](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).

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

## <a name="js-modules"></a>Moduły JS

W przypadku izolacji JS program JS współdziała z domyślną obsługą języka [ECMAScript (](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) [model ECMAScript](https://tc39.es/ecma262/#sec-modules)) w przeglądarce.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/call-javascript-from-dotnet>
* [`InteropComponent.razor` przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałęzi wydania)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
