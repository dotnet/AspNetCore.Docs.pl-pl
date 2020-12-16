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
ms.openlocfilehash: c1a97919cb41f42a93f28d9b5f1ecf6bd3e64da0
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97592859"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="89f5f-103">Wywoływanie metod .NET z funkcji języka JavaScript w ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="89f5f-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="89f5f-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)i [Luke](https://github.com/guardrex) Latham</span><span class="sxs-lookup"><span data-stu-id="89f5f-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="89f5f-105">BlazorAplikacja może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="89f5f-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="89f5f-106">Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie *js Interop*).</span><span class="sxs-lookup"><span data-stu-id="89f5f-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="89f5f-107">W tym artykule opisano wywoływanie metod .NET z języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="89f5f-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="89f5f-108">Aby uzyskać informacje na temat wywoływania funkcji JavaScript z platformy .NET, zobacz <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="89f5f-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="89f5f-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="89f5f-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="89f5f-110">Statyczne wywołanie metody .NET</span><span class="sxs-lookup"><span data-stu-id="89f5f-110">Static .NET method call</span></span>

<span data-ttu-id="89f5f-111">Aby wywołać statyczną metodę .NET z poziomu języka JavaScript, `DotNet.invokeMethod` Użyj `DotNet.invokeMethodAsync` funkcji or.</span><span class="sxs-lookup"><span data-stu-id="89f5f-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="89f5f-112">Przekaż identyfikator metody statycznej, która ma być wywoływana, nazwę zestawu zawierającego funkcję i wszelkie argumenty.</span><span class="sxs-lookup"><span data-stu-id="89f5f-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="89f5f-113">Wersja asynchroniczna jest preferowana do obsługi Blazor Server scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="89f5f-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="89f5f-114">Metoda .NET musi być publiczna, statyczna i mieć [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atrybut.</span><span class="sxs-lookup"><span data-stu-id="89f5f-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="89f5f-115">Wywoływanie otwartych metod ogólnych nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="89f5f-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="89f5f-116">Przykładowa aplikacja zawiera metodę języka C#, która zwraca `int` tablicę.</span><span class="sxs-lookup"><span data-stu-id="89f5f-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="89f5f-117">Ten [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atrybut jest stosowany do metody.</span><span class="sxs-lookup"><span data-stu-id="89f5f-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="89f5f-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="89f5f-119">Kod JavaScript obsługiwany przez klienta wywołuje metodę języka C# .NET.</span><span class="sxs-lookup"><span data-stu-id="89f5f-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="89f5f-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="89f5f-121">Po **`Trigger .NET static method ReturnArrayAsync`** wybraniu przycisku Sprawdź dane wyjściowe konsoli w narzędziach deweloperskich sieci Web w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="89f5f-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="89f5f-122">Dane wyjściowe konsoli są następujące:</span><span class="sxs-lookup"><span data-stu-id="89f5f-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="89f5f-123">Czwarta wartość tablicy jest wypychana do tablicy ( `data.push(4);` ) zwracanej przez `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="89f5f-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="89f5f-124">Domyślnie identyfikator metody jest nazwą metody, ale można określić inny identyfikator przy użyciu [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) konstruktora atrybutu:</span><span class="sxs-lookup"><span data-stu-id="89f5f-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="89f5f-125">W pliku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="89f5f-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="89f5f-126">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="89f5f-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="89f5f-127">Wywołanie metody wystąpienia</span><span class="sxs-lookup"><span data-stu-id="89f5f-127">Instance method call</span></span>

<span data-ttu-id="89f5f-128">Można również wywołać metody wystąpienia platformy .NET z poziomu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="89f5f-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="89f5f-129">Aby wywołać metodę wystąpienia platformy .NET z poziomu języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="89f5f-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="89f5f-130">Przekaż wystąpienie platformy .NET przez odwołanie do języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="89f5f-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="89f5f-131">Wykonaj wywołanie statyczne do <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="89f5f-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="89f5f-132">Zawiń wystąpienie w <xref:Microsoft.JSInterop.DotNetObjectReference> wystąpieniu i Wywołaj wystąpienie <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> <xref:Microsoft.JSInterop.DotNetObjectReference> .</span><span class="sxs-lookup"><span data-stu-id="89f5f-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="89f5f-133">Usuwanie <xref:Microsoft.JSInterop.DotNetObjectReference> obiektów (przykład pojawia się w dalszej części tej sekcji).</span><span class="sxs-lookup"><span data-stu-id="89f5f-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="89f5f-134">Wywołaj metody wystąpienia platformy .NET w wystąpieniu przy `invokeMethod` użyciu `invokeMethodAsync` funkcji lub.</span><span class="sxs-lookup"><span data-stu-id="89f5f-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="89f5f-135">Wystąpienie programu .NET może być również przekazywać jako argument podczas wywoływania innych metod .NET z JavaScript.</span><span class="sxs-lookup"><span data-stu-id="89f5f-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="89f5f-136">Przykładowa aplikacja rejestruje komunikaty do konsoli po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="89f5f-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="89f5f-137">W poniższych przykładach zademonstrowanych przez przykładową aplikację można sprawdzić dane wyjściowe konsoli przeglądarki w narzędziach deweloperskich w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="89f5f-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="89f5f-138">Gdy **`Trigger .NET instance method HelloHelper.SayHello`** przycisk jest zaznaczony, `ExampleJsInterop.CallHelloHelperSayHello` jest wywoływana i przekazuje nazwę, `Blazor` do metody.</span><span class="sxs-lookup"><span data-stu-id="89f5f-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="89f5f-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-139">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="89f5f-140">`CallHelloHelperSayHello` wywołuje funkcję JavaScript `sayHello` z nowym wystąpieniem `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="89f5f-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="89f5f-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="89f5f-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="89f5f-143">Nazwa jest przenoszona do `HelloHelper` konstruktora, który ustawia `HelloHelper.Name` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="89f5f-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="89f5f-144">Gdy funkcja JavaScript `sayHello` jest wykonywana, `HelloHelper.SayHello` zwraca `Hello, {Name}!` komunikat, który jest zapisywana w konsoli przez funkcję JavaScript.</span><span class="sxs-lookup"><span data-stu-id="89f5f-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="89f5f-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="89f5f-146">Dane wyjściowe konsoli w narzędziach deweloperskich sieci Web w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="89f5f-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="89f5f-147">Aby uniknąć przecieków pamięci i zezwolić na wyrzucanie elementów bezużytecznych w składniku, który tworzy <xref:Microsoft.JSInterop.DotNetObjectReference> , należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="89f5f-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="89f5f-148">Usuń obiekt z klasy, która utworzyła <xref:Microsoft.JSInterop.DotNetObjectReference> wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="89f5f-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="89f5f-149">Poprzedni wzorzec przedstawiony w `ExampleJsInterop` klasie może być również zaimplementowany w składniku:</span><span class="sxs-lookup"><span data-stu-id="89f5f-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
  
  <span data-ttu-id="89f5f-150">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="89f5f-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="89f5f-151">Gdy składnik lub Klasa nie usuwa <xref:Microsoft.JSInterop.DotNetObjectReference> metody Dispose z klienta przez wywołanie `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="89f5f-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="89f5f-152">Wywołanie metody wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="89f5f-152">Component instance method call</span></span>

<span data-ttu-id="89f5f-153">Aby wywołać metody .NET składnika:</span><span class="sxs-lookup"><span data-stu-id="89f5f-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="89f5f-154">Użyj `invokeMethod` funkcji or, `invokeMethodAsync` Aby wykonać wywołanie metody statycznej do składnika.</span><span class="sxs-lookup"><span data-stu-id="89f5f-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="89f5f-155">Metoda statyczna składnika zawija wywołanie do metody instancji jako wywołane <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="89f5f-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="89f5f-156">W przypadku Blazor Server aplikacji, w których kilku użytkowników może jednocześnie korzystać z tego samego składnika, użyj klasy pomocnika, aby wywołać metody instancji.</span><span class="sxs-lookup"><span data-stu-id="89f5f-156">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="89f5f-157">Aby uzyskać więcej informacji, zobacz sekcję [Klasa pomocnika metody wystąpienia składnika](#component-instance-method-helper-class) .</span><span class="sxs-lookup"><span data-stu-id="89f5f-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="89f5f-158">W języku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="89f5f-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="89f5f-159">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="89f5f-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="89f5f-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-160">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="89f5f-161">Aby przekazać argumenty do metody wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="89f5f-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="89f5f-162">Dodaj parametry do wywołania metody JS.</span><span class="sxs-lookup"><span data-stu-id="89f5f-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="89f5f-163">W poniższym przykładzie nazwa jest przenoszona do metody.</span><span class="sxs-lookup"><span data-stu-id="89f5f-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="89f5f-164">W razie potrzeby można dodać do listy dodatkowe parametry.</span><span class="sxs-lookup"><span data-stu-id="89f5f-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="89f5f-165">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="89f5f-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="89f5f-166">Podaj prawidłowe typy <xref:System.Action> dla parametrów.</span><span class="sxs-lookup"><span data-stu-id="89f5f-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="89f5f-167">Podaj listę parametrów w metodach języka C#.</span><span class="sxs-lookup"><span data-stu-id="89f5f-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="89f5f-168">Wywołaj <xref:System.Action> ( `UpdateMessage` ) z parametrami ( `action.Invoke(name)` ).</span><span class="sxs-lookup"><span data-stu-id="89f5f-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="89f5f-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-169">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="89f5f-170">Wyjście `message` , gdy zostanie wybrany przycisk **metody wywołania js** :</span><span class="sxs-lookup"><span data-stu-id="89f5f-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="89f5f-171">Klasa pomocnika metody wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="89f5f-171">Component instance method helper class</span></span>

<span data-ttu-id="89f5f-172">Klasa pomocnika służy do wywoływania metody wystąpienia jako <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="89f5f-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="89f5f-173">Klasy pomocnika są przydatne w przypadku:</span><span class="sxs-lookup"><span data-stu-id="89f5f-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="89f5f-174">Niektóre składniki tego samego typu są renderowane na tej samej stronie.</span><span class="sxs-lookup"><span data-stu-id="89f5f-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="89f5f-175">Blazor ServerUżywana jest aplikacja, w której wielu użytkowników może używać składnika współbieżnie.</span><span class="sxs-lookup"><span data-stu-id="89f5f-175">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="89f5f-176">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="89f5f-176">In the following example:</span></span>

* <span data-ttu-id="89f5f-177">`JSInteropExample`Składnik zawiera kilka `ListItem` składników.</span><span class="sxs-lookup"><span data-stu-id="89f5f-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="89f5f-178">Każdy `ListItem` składnik składa się z komunikatu i przycisku.</span><span class="sxs-lookup"><span data-stu-id="89f5f-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="89f5f-179">Po `ListItem` wybraniu przycisku składnik `ListItem` `UpdateMessage` Metoda zmienia tekst elementu listy i ukrywa przycisk.</span><span class="sxs-lookup"><span data-stu-id="89f5f-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="89f5f-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-180">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="89f5f-181">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="89f5f-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="89f5f-182">W języku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="89f5f-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="89f5f-183">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="89f5f-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="89f5f-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-184">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="89f5f-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="89f5f-185">`Pages/JSInteropExample.razor`:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="89f5f-186">Unikaj cyklicznych odwołań do obiektów</span><span class="sxs-lookup"><span data-stu-id="89f5f-186">Avoid circular object references</span></span>

<span data-ttu-id="89f5f-187">Obiekty, które zawierają odwołania cykliczne, nie mogą być serializowane na kliencie dla obu:</span><span class="sxs-lookup"><span data-stu-id="89f5f-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="89f5f-188">Wywołania metody .NET.</span><span class="sxs-lookup"><span data-stu-id="89f5f-188">.NET method calls.</span></span>
* <span data-ttu-id="89f5f-189">Wywołania metody JavaScript z języka C#, gdy typem zwracanym są odwołania cykliczne.</span><span class="sxs-lookup"><span data-stu-id="89f5f-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="89f5f-190">Aby uzyskać więcej informacji, zobacz następujące problemy:</span><span class="sxs-lookup"><span data-stu-id="89f5f-190">For more information, see the following issues:</span></span>

* [<span data-ttu-id="89f5f-191">Odwołania cykliczne nie są obsługiwane, zrób dwa (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="89f5f-191">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="89f5f-192">Propozycja: Dodawanie mechanizmu do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/Runtime)</span><span class="sxs-lookup"><span data-stu-id="89f5f-192">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="js-modules"></a><span data-ttu-id="89f5f-193">Moduły JS</span><span class="sxs-lookup"><span data-stu-id="89f5f-193">JS modules</span></span>

<span data-ttu-id="89f5f-194">W przypadku izolacji JS program JS współdziała z domyślną obsługą języka [ECMAScript (](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) [model ECMAScript](https://tc39.es/ecma262/#sec-modules)) w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="89f5f-194">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89f5f-195">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="89f5f-195">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="89f5f-196">`InteropComponent.razor` przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałęzi wydania)</span><span class="sxs-lookup"><span data-stu-id="89f5f-196">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
