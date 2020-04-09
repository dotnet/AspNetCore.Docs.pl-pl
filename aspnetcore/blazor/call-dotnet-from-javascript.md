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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="db652-103">Wywoływanie metod platformy .NET z funkcji JavaScript w ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="db652-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="db652-104">[Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="db652-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="db652-105">Aplikacja Blazor może wywoływać funkcje JavaScript z metod .NET i .NET z funkcji JavaScript.</span><span class="sxs-lookup"><span data-stu-id="db652-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="db652-106">Scenariusze te są nazywane *interoperacyjnością JavaScript* *(JS interop*).</span><span class="sxs-lookup"><span data-stu-id="db652-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="db652-107">W tym artykule omówiono wywoływanie metod platformy .NET z języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="db652-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="db652-108">Aby uzyskać informacje na temat wywoływania funkcji <xref:blazor/call-javascript-from-dotnet>JavaScript z platformy .NET, zobacz .</span><span class="sxs-lookup"><span data-stu-id="db652-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="db652-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db652-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="db652-110">Statyczne wywołanie metody .NET</span><span class="sxs-lookup"><span data-stu-id="db652-110">Static .NET method call</span></span>

<span data-ttu-id="db652-111">Aby wywołać statyczną metodę .NET z `DotNet.invokeMethod` języka `DotNet.invokeMethodAsync` JavaScript, należy użyć funkcji lub.</span><span class="sxs-lookup"><span data-stu-id="db652-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="db652-112">Przekaż w identyfikatorzie metody statycznej, którą chcesz wywołać, nazwę zestawu zawierającego funkcję i wszelkie argumenty.</span><span class="sxs-lookup"><span data-stu-id="db652-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="db652-113">Wersja asynchroniowa jest preferowana do obsługi Blazor scenariuszy serwera.</span><span class="sxs-lookup"><span data-stu-id="db652-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="db652-114">Metoda .NET musi być publiczna, statyczna i mieć `[JSInvokable]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="db652-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="db652-115">Wywoływanie otwartych metod ogólnych nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="db652-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="db652-116">Przykładowa aplikacja zawiera metodę języka `int` C#, aby zwrócić tablicę.</span><span class="sxs-lookup"><span data-stu-id="db652-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="db652-117">Atrybut `JSInvokable` jest stosowany do metody.</span><span class="sxs-lookup"><span data-stu-id="db652-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="db652-118">*Strony/JsInterop.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="db652-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="db652-119">JavaScript obsługiwany klientowi wywołuje metodę C# .NET.</span><span class="sxs-lookup"><span data-stu-id="db652-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="db652-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="db652-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="db652-121">Po wybraniu przycisku **Trigger .NET metoda statyczna ReturnArrayAsync,** sprawdź dane wyjściowe konsoli w narzędziach programistycznych sieci web przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="db652-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="db652-122">Wyjście konsoli jest następujące:</span><span class="sxs-lookup"><span data-stu-id="db652-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="db652-123">Czwarta wartość tablicy jest wypychany do tablicy (`data.push(4);`) zwracany przez `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="db652-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="db652-124">Domyślnie identyfikator metody jest nazwą metody, ale można określić inny `JSInvokableAttribute` identyfikator przy użyciu konstruktora:</span><span class="sxs-lookup"><span data-stu-id="db652-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="db652-125">W pliku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="db652-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="db652-126">Wywołanie metody wystąpienia</span><span class="sxs-lookup"><span data-stu-id="db652-126">Instance method call</span></span>

<span data-ttu-id="db652-127">Można również wywołać metody wystąpienia platformy .NET z języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="db652-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="db652-128">Aby wywołać metodę wystąpienia platformy .NET z języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="db652-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="db652-129">Przekaż wystąpienie .NET przez odwołanie do języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="db652-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="db652-130">Nawiązać statyczne `DotNetObjectReference.Create`wywołanie do pliku .</span><span class="sxs-lookup"><span data-stu-id="db652-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="db652-131">Zawiń wystąpienie `DotNetObjectReference` w `Create` wystąpieniu `DotNetObjectReference` i wywołaj wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="db652-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="db652-132">Zutylizuj `DotNetObjectReference` obiekty (przykład pojawia się w dalszej części tej sekcji).</span><span class="sxs-lookup"><span data-stu-id="db652-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="db652-133">Wywołaj metody wystąpienia .NET w `invokeMethod` `invokeMethodAsync` wystąpieniu przy użyciu funkcji lub.</span><span class="sxs-lookup"><span data-stu-id="db652-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="db652-134">Wystąpienie platformy .NET można również przekazać jako argument podczas wywoływania innych metod platformy .NET z języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="db652-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="db652-135">Przykładowa aplikacja rejestruje komunikaty do konsoli po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="db652-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="db652-136">Poniższe przykłady zademonstrowane przez przykładową aplikację należy sprawdzić dane wyjściowe konsoli przeglądarki w narzędziach deweloperskich przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="db652-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="db652-137">Gdy **trigger .NET metody wystąpienia HelloHelper.SayHello** przycisk jest zaznaczony, `ExampleJsInterop.CallHelloHelperSayHello` jest wywoływana i przekazuje nazwę, `Blazor`do metody.</span><span class="sxs-lookup"><span data-stu-id="db652-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="db652-138">*Strony/JsInterop.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="db652-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="db652-139">`CallHelloHelperSayHello`wywołuje funkcję `sayHello` JavaScript z nowym `HelloHelper`wystąpieniem .</span><span class="sxs-lookup"><span data-stu-id="db652-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="db652-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="db652-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="db652-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="db652-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="db652-142">Nazwa jest przekazywana do `HelloHelper`konstruktora , który ustawia `HelloHelper.Name` właściwość.</span><span class="sxs-lookup"><span data-stu-id="db652-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="db652-143">Po wykonaniu funkcji `sayHello` JavaScript `HelloHelper.SayHello` zwraca `Hello, {Name}!` komunikat, który jest zapisywany na konsoli przez funkcję JavaScript.</span><span class="sxs-lookup"><span data-stu-id="db652-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="db652-144">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="db652-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="db652-145">Dane wyjściowe konsoli w narzędziach programistycznych przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="db652-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="db652-146">Aby uniknąć przecieku pamięci i zezwolić na `DotNetObjectReference`wyrzucanie elementów bezużytecznych na składniku, który tworzy , przyjąć jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="db652-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="db652-147">Zutylizuj obiekt w `DotNetObjectReference` klasie, która utworzyła wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="db652-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="db652-148">Poprzedni wzorzec pokazany `ExampleJsInterop` w klasie można również zaimplementować w komponencie:</span><span class="sxs-lookup"><span data-stu-id="db652-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="db652-149">Jeśli składnik lub klasa nie pozbywa się `DotNetObjectReference`, zbywaj obiekt `.dispose()`na kliencie, wywołując:</span><span class="sxs-lookup"><span data-stu-id="db652-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="db652-150">Wywołanie metody wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="db652-150">Component instance method call</span></span>

<span data-ttu-id="db652-151">Aby wywołać metody .NET składnika:</span><span class="sxs-lookup"><span data-stu-id="db652-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="db652-152">Użyj `invokeMethod` funkcji `invokeMethodAsync` lub, aby wywołać metodę statyczną do składnika.</span><span class="sxs-lookup"><span data-stu-id="db652-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="db652-153">Metoda statyczna składnika zawija wywołanie metody wystąpienia `Action`jako wywoływane .</span><span class="sxs-lookup"><span data-stu-id="db652-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="db652-154">W języku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="db652-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="db652-155">*Strony/JSInteropComponent.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="db652-155">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="db652-156">Gdy istnieje kilka składników, każdy z metod wystąpienia do wywołania, należy użyć `Action`klasy pomocnika do wywołania metody wystąpienia (jako s) każdego składnika.</span><span class="sxs-lookup"><span data-stu-id="db652-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="db652-157">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="db652-157">In the following example:</span></span>

* <span data-ttu-id="db652-158">Składnik `JSInterop` zawiera `ListItem` kilka składników.</span><span class="sxs-lookup"><span data-stu-id="db652-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="db652-159">Każdy `ListItem` składnik składa się z wiadomości i przycisku.</span><span class="sxs-lookup"><span data-stu-id="db652-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="db652-160">Po `ListItem` wybraniu przycisku `ListItem`komponentu `UpdateMessage` metoda ta zmienia tekst elementu listy i ukrywa przycisk.</span><span class="sxs-lookup"><span data-stu-id="db652-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="db652-161">*MessageUpdateInvokeHelper.cs:*</span><span class="sxs-lookup"><span data-stu-id="db652-161">*MessageUpdateInvokeHelper.cs*:</span></span>

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

<span data-ttu-id="db652-162">W języku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="db652-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="db652-163">*Shared/ListItem.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="db652-163">*Shared/ListItem.razor*:</span></span>

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

<span data-ttu-id="db652-164">*Strony/JSInterop.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="db652-164">*Pages/JSInterop.razor*:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="db652-165">Unikaj odwołań do obiektów cyklicznych</span><span class="sxs-lookup"><span data-stu-id="db652-165">Avoid circular object references</span></span>

<span data-ttu-id="db652-166">Obiekty, które zawierają odwołania cykliczne nie mogą być serializowane na kliencie dla jednego z:</span><span class="sxs-lookup"><span data-stu-id="db652-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="db652-167">Wywołania metody .NET.</span><span class="sxs-lookup"><span data-stu-id="db652-167">.NET method calls.</span></span>
* <span data-ttu-id="db652-168">Metoda JavaScript wywołuje z języka C#, gdy typ zwracany ma odwołania cykliczne.</span><span class="sxs-lookup"><span data-stu-id="db652-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="db652-169">Aby uzyskać więcej informacji, zobacz następujące problemy:</span><span class="sxs-lookup"><span data-stu-id="db652-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="db652-170">Odwołania cykliczne nie są obsługiwane, weź dwa (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="db652-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="db652-171">Propozycja: Dodaj mechanizm do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/runtime)</span><span class="sxs-lookup"><span data-stu-id="db652-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="db652-172">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="db652-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="db652-173">Przykład InteropComponent.razor (repozytorium Dotnet/AspNetCore GitHub, gałąź wydania 3.1)</span><span class="sxs-lookup"><span data-stu-id="db652-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="db652-174">[Wykonywanie dużych transferów danych w Blazor aplikacjach serwera](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="db652-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
