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
ms.openlocfilehash: 45ddcc9e006df2c5e86a7859efc76882b269a496
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280394"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="e9284-103">Wywoływanie metod .NET z funkcji języka JavaScript w ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e9284-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="e9284-104">BlazorAplikacja może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9284-104">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="e9284-105">Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie *js Interop*).</span><span class="sxs-lookup"><span data-stu-id="e9284-105">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="e9284-106">W tym artykule opisano wywoływanie metod .NET z języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9284-106">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="e9284-107">Aby uzyskać informacje na temat wywoływania funkcji JavaScript z platformy .NET, zobacz <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="e9284-107">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="e9284-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e9284-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="e9284-109">Dodaj pliki JS ( `<script>` tagi) przed tagiem zamykającym `</body>` w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="e9284-109">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="e9284-110">Upewnij się, że pliki JS z metodami międzyoperacyjnymi JS są uwzględnione przed Blazor plikami Framework js.</span><span class="sxs-lookup"><span data-stu-id="e9284-110">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="e9284-111">Statyczne wywołanie metody .NET</span><span class="sxs-lookup"><span data-stu-id="e9284-111">Static .NET method call</span></span>

<span data-ttu-id="e9284-112">Aby wywołać statyczną metodę .NET z poziomu języka JavaScript, `DotNet.invokeMethod` Użyj `DotNet.invokeMethodAsync` funkcji or.</span><span class="sxs-lookup"><span data-stu-id="e9284-112">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="e9284-113">Przekaż identyfikator metody statycznej, która ma być wywoływana, nazwę zestawu zawierającego funkcję i wszelkie argumenty.</span><span class="sxs-lookup"><span data-stu-id="e9284-113">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="e9284-114">Wersja asynchroniczna jest preferowana do obsługi Blazor Server scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="e9284-114">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="e9284-115">Metoda .NET musi być publiczna, statyczna i mieć [ `[JSInvokable]` atrybut](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="e9284-115">The .NET method must be public, static, and have the [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute).</span></span> <span data-ttu-id="e9284-116">Wywoływanie otwartych metod ogólnych nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="e9284-116">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="e9284-117">Przykładowa aplikacja zawiera metodę języka C#, która zwraca `int` tablicę.</span><span class="sxs-lookup"><span data-stu-id="e9284-117">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="e9284-118">Ten [ `[JSInvokable]` atrybut](xref:Microsoft.JSInterop.JSInvokableAttribute) jest stosowany do metody.</span><span class="sxs-lookup"><span data-stu-id="e9284-118">The [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute) is applied to the method.</span></span>

<span data-ttu-id="e9284-119">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="e9284-119">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="e9284-120">Kod JavaScript obsługiwany przez klienta wywołuje metodę języka C# .NET.</span><span class="sxs-lookup"><span data-stu-id="e9284-120">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="e9284-121">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="e9284-121">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="e9284-122">Po **`Trigger .NET static method ReturnArrayAsync`** wybraniu przycisku Sprawdź dane wyjściowe konsoli w narzędziach deweloperskich sieci Web w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="e9284-122">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="e9284-123">Dane wyjściowe konsoli są następujące:</span><span class="sxs-lookup"><span data-stu-id="e9284-123">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="e9284-124">Czwarta wartość tablicy jest wypychana do tablicy ( `data.push(4);` ) zwracanej przez `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="e9284-124">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="e9284-125">Domyślnie identyfikator metody jest nazwą metody, ale można określić inny identyfikator przy użyciu konstruktora [ `[JSInvokable]` atrybutu](xref:Microsoft.JSInterop.JSInvokableAttribute) :</span><span class="sxs-lookup"><span data-stu-id="e9284-125">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute) constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="e9284-126">W pliku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="e9284-126">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="e9284-127">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="e9284-127">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="e9284-128">Wywołanie metody wystąpienia</span><span class="sxs-lookup"><span data-stu-id="e9284-128">Instance method call</span></span>

<span data-ttu-id="e9284-129">Można również wywołać metody wystąpienia platformy .NET z poziomu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9284-129">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="e9284-130">Aby wywołać metodę wystąpienia platformy .NET z poziomu języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="e9284-130">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="e9284-131">Przekaż wystąpienie platformy .NET przez odwołanie do języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="e9284-131">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="e9284-132">Wykonaj wywołanie statyczne do <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="e9284-132">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="e9284-133">Zawiń wystąpienie w <xref:Microsoft.JSInterop.DotNetObjectReference> wystąpieniu i Wywołaj wystąpienie <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> <xref:Microsoft.JSInterop.DotNetObjectReference> .</span><span class="sxs-lookup"><span data-stu-id="e9284-133">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="e9284-134">Usuwanie <xref:Microsoft.JSInterop.DotNetObjectReference> obiektów (przykład pojawia się w dalszej części tej sekcji).</span><span class="sxs-lookup"><span data-stu-id="e9284-134">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="e9284-135">Wywołaj metody wystąpienia platformy .NET w wystąpieniu przy `invokeMethod` użyciu `invokeMethodAsync` funkcji lub.</span><span class="sxs-lookup"><span data-stu-id="e9284-135">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="e9284-136">Wystąpienie programu .NET może być również przekazywać jako argument podczas wywoływania innych metod .NET z JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9284-136">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="e9284-137">Przykładowa aplikacja rejestruje komunikaty do konsoli po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="e9284-137">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="e9284-138">W poniższych przykładach zademonstrowanych przez przykładową aplikację można sprawdzić dane wyjściowe konsoli przeglądarki w narzędziach deweloperskich w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="e9284-138">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="e9284-139">Gdy **`Trigger .NET instance method HelloHelper.SayHello`** przycisk jest zaznaczony, `ExampleJsInterop.CallHelloHelperSayHello` jest wywoływana i przekazuje nazwę, `Blazor` do metody.</span><span class="sxs-lookup"><span data-stu-id="e9284-139">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="e9284-140">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="e9284-140">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="e9284-141">`CallHelloHelperSayHello` wywołuje funkcję JavaScript `sayHello` z nowym wystąpieniem `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="e9284-141">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="e9284-142">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="e9284-142">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="e9284-143">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="e9284-143">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="e9284-144">Nazwa jest przenoszona do `HelloHelper` konstruktora, który ustawia `HelloHelper.Name` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="e9284-144">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="e9284-145">Gdy funkcja JavaScript `sayHello` jest wykonywana, `HelloHelper.SayHello` zwraca `Hello, {Name}!` komunikat, który jest zapisywana w konsoli przez funkcję JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9284-145">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="e9284-146">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="e9284-146">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="e9284-147">Dane wyjściowe konsoli w narzędziach deweloperskich sieci Web w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="e9284-147">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="e9284-148">Aby uniknąć przecieków pamięci i zezwolić na wyrzucanie elementów bezużytecznych w składniku, który tworzy <xref:Microsoft.JSInterop.DotNetObjectReference> , należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="e9284-148">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="e9284-149">Usuń obiekt z klasy, która utworzyła <xref:Microsoft.JSInterop.DotNetObjectReference> wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="e9284-149">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="e9284-150">Poprzedni wzorzec przedstawiony w `ExampleJsInterop` klasie może być również zaimplementowany w składniku:</span><span class="sxs-lookup"><span data-stu-id="e9284-150">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
  
  <span data-ttu-id="e9284-151">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="e9284-151">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="e9284-152">Gdy składnik lub Klasa nie usuwa <xref:Microsoft.JSInterop.DotNetObjectReference> metody Dispose z klienta przez wywołanie `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="e9284-152">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="e9284-153">Wywołanie metody wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="e9284-153">Component instance method call</span></span>

<span data-ttu-id="e9284-154">Aby wywołać metody .NET składnika:</span><span class="sxs-lookup"><span data-stu-id="e9284-154">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="e9284-155">Użyj `invokeMethod` funkcji or, `invokeMethodAsync` Aby wykonać wywołanie metody statycznej do składnika.</span><span class="sxs-lookup"><span data-stu-id="e9284-155">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="e9284-156">Metoda statyczna składnika zawija wywołanie do metody instancji jako wywołane <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="e9284-156">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="e9284-157">W przypadku Blazor Server aplikacji, w których kilku użytkowników może jednocześnie korzystać z tego samego składnika, użyj klasy pomocnika, aby wywołać metody instancji.</span><span class="sxs-lookup"><span data-stu-id="e9284-157">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="e9284-158">Aby uzyskać więcej informacji, zobacz sekcję [Klasa pomocnika metody wystąpienia składnika](#component-instance-method-helper-class) .</span><span class="sxs-lookup"><span data-stu-id="e9284-158">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="e9284-159">W języku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="e9284-159">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="e9284-160">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="e9284-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="e9284-161">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e9284-161">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="e9284-162">Aby przekazać argumenty do metody wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="e9284-162">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="e9284-163">Dodaj parametry do wywołania metody JS.</span><span class="sxs-lookup"><span data-stu-id="e9284-163">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="e9284-164">W poniższym przykładzie nazwa jest przenoszona do metody.</span><span class="sxs-lookup"><span data-stu-id="e9284-164">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="e9284-165">W razie potrzeby można dodać do listy dodatkowe parametry.</span><span class="sxs-lookup"><span data-stu-id="e9284-165">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="e9284-166">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="e9284-166">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="e9284-167">Podaj prawidłowe typy <xref:System.Action> dla parametrów.</span><span class="sxs-lookup"><span data-stu-id="e9284-167">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="e9284-168">Podaj listę parametrów w metodach języka C#.</span><span class="sxs-lookup"><span data-stu-id="e9284-168">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="e9284-169">Wywołaj <xref:System.Action> ( `UpdateMessage` ) z parametrami ( `action.Invoke(name)` ).</span><span class="sxs-lookup"><span data-stu-id="e9284-169">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="e9284-170">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e9284-170">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="e9284-171">Wyjście `message` , gdy zostanie wybrany przycisk **metody wywołania js** :</span><span class="sxs-lookup"><span data-stu-id="e9284-171">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="e9284-172">Klasa pomocnika metody wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="e9284-172">Component instance method helper class</span></span>

<span data-ttu-id="e9284-173">Klasa pomocnika służy do wywoływania metody wystąpienia jako <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="e9284-173">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="e9284-174">Klasy pomocnika są przydatne w przypadku:</span><span class="sxs-lookup"><span data-stu-id="e9284-174">Helper classes are useful when:</span></span>

* <span data-ttu-id="e9284-175">Niektóre składniki tego samego typu są renderowane na tej samej stronie.</span><span class="sxs-lookup"><span data-stu-id="e9284-175">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="e9284-176">Blazor ServerUżywana jest aplikacja, w której wielu użytkowników może używać składnika współbieżnie.</span><span class="sxs-lookup"><span data-stu-id="e9284-176">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="e9284-177">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="e9284-177">In the following example:</span></span>

* <span data-ttu-id="e9284-178">`JSInteropExample`Składnik zawiera kilka `ListItem` składników.</span><span class="sxs-lookup"><span data-stu-id="e9284-178">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="e9284-179">Każdy `ListItem` składnik składa się z komunikatu i przycisku.</span><span class="sxs-lookup"><span data-stu-id="e9284-179">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="e9284-180">Po `ListItem` wybraniu przycisku składnik `ListItem` `UpdateMessage` Metoda zmienia tekst elementu listy i ukrywa przycisk.</span><span class="sxs-lookup"><span data-stu-id="e9284-180">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="e9284-181">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="e9284-181">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="e9284-182">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="e9284-182">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="e9284-183">W języku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="e9284-183">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="e9284-184">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="e9284-184">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="e9284-185">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="e9284-185">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="e9284-186">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="e9284-186">`Pages/JSInteropExample.razor`:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="e9284-187">Unikaj cyklicznych odwołań do obiektów</span><span class="sxs-lookup"><span data-stu-id="e9284-187">Avoid circular object references</span></span>

<span data-ttu-id="e9284-188">Obiekty, które zawierają odwołania cykliczne, nie mogą być serializowane na kliencie dla obu:</span><span class="sxs-lookup"><span data-stu-id="e9284-188">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="e9284-189">Wywołania metody .NET.</span><span class="sxs-lookup"><span data-stu-id="e9284-189">.NET method calls.</span></span>
* <span data-ttu-id="e9284-190">Wywołania metody JavaScript z języka C#, gdy typem zwracanym są odwołania cykliczne.</span><span class="sxs-lookup"><span data-stu-id="e9284-190">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="e9284-191">Aby uzyskać więcej informacji, zobacz następujące problemy:</span><span class="sxs-lookup"><span data-stu-id="e9284-191">For more information, see the following issues:</span></span>

* [<span data-ttu-id="e9284-192">Odwołania cykliczne nie są obsługiwane, zrób dwa (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="e9284-192">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="e9284-193">Propozycja: Dodawanie mechanizmu do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/Runtime)</span><span class="sxs-lookup"><span data-stu-id="e9284-193">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="e9284-194">Limity rozmiaru dla wywołań międzyoperacyjnych w JS</span><span class="sxs-lookup"><span data-stu-id="e9284-194">Size limits on JS interop calls</span></span>

<span data-ttu-id="e9284-195">W programie Blazor WebAssembly platforma nie nakłada limitu rozmiaru danych wejściowych i wyjściowych w programie js.</span><span class="sxs-lookup"><span data-stu-id="e9284-195">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="e9284-196">W programie Blazor Server wywołania programu js Interop są ograniczone przez maksymalny SignalR rozmiar przychodzących komunikatów dozwolony dla metod koncentratora, które są wymuszane przez <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (domyślnie: 32 KB).</span><span class="sxs-lookup"><span data-stu-id="e9284-196">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="e9284-197">Program JS do SignalR komunikatów programu .NET większych niż <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> Zgłoś błąd.</span><span class="sxs-lookup"><span data-stu-id="e9284-197">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="e9284-198">Platforma nie nakłada limitu rozmiaru SignalR komunikatu z centrum na klienta.</span><span class="sxs-lookup"><span data-stu-id="e9284-198">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="e9284-199">Gdy SignalR Rejestrowanie nie jest ustawione na [debugowanie](xref:Microsoft.Extensions.Logging.LogLevel) lub [śledzenie](xref:Microsoft.Extensions.Logging.LogLevel), Błąd rozmiaru komunikatu pojawia się tylko w konsoli narzędzia deweloperskie w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="e9284-199">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="e9284-200">Błąd: połączenie zostało rozłączone z błędem "błąd: serwer zwrócił błąd podczas zamykania: połączenie zostało zamknięte z błędem".</span><span class="sxs-lookup"><span data-stu-id="e9284-200">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="e9284-201">Gdy [ SignalR Rejestrowanie po stronie serwera](xref:signalr/diagnostics#server-side-logging) jest ustawione na [debugowanie](xref:Microsoft.Extensions.Logging.LogLevel) lub [śledzenie](xref:Microsoft.Extensions.Logging.LogLevel), podczas rejestrowania po stronie serwera jest wyświetlany <xref:System.IO.InvalidDataException> komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="e9284-201">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="e9284-202">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="e9284-202">`appsettings.Development.json`:</span></span>

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

> <span data-ttu-id="e9284-203">System. IO. InvalidDataException —: przekroczono maksymalny rozmiar komunikatu 32768B.</span><span class="sxs-lookup"><span data-stu-id="e9284-203">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="e9284-204">Rozmiar komunikatu można skonfigurować w AddHubOptions.</span><span class="sxs-lookup"><span data-stu-id="e9284-204">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="e9284-205">Zwiększ limit przez ustawienie <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e9284-205">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e9284-206">W poniższym przykładzie ustawiono maksymalny rozmiar komunikatu odbioru na 64 KB (64 \* 1024):</span><span class="sxs-lookup"><span data-stu-id="e9284-206">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="e9284-207">Zwiększenie SignalR limitu rozmiaru komunikatów przychodzących jest kosztem wymagania większej liczby zasobów serwera i ujawnia serwerowi zwiększone ryzyko ze strony złośliwego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e9284-207">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="e9284-208">Ponadto odczytywanie dużej ilości zawartości w usłudze do pamięci jako ciągów lub tablic bajtowych może również spowodować, że alokacje działające nieprawidłowo z modułem wyrzucania elementów bezużytecznych, co skutkuje dodatkowymi karami za wydajność.</span><span class="sxs-lookup"><span data-stu-id="e9284-208">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="e9284-209">Jedną z opcji odczytu dużych ładunków jest wysłanie zawartości w mniejszych fragmentach i przetworzenie ładunku jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="e9284-209">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="e9284-210">Można go użyć podczas odczytywania dużych ładunków JSON lub w przypadku, gdy dane są dostępne w języku JavaScript jako nieprzetworzone bajty.</span><span class="sxs-lookup"><span data-stu-id="e9284-210">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="e9284-211">Przykład demonstrujący wysyłanie dużych ładunków binarnych w programie Blazor Server , które używają technik podobnych do `InputFile` składnika, można znaleźć w [przykładowej aplikacji przesyłania danych binarnych](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="e9284-211">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="e9284-212">Podczas opracowywania kodu, który przesyła dużą ilość danych między językami JavaScript i należy wziąć pod uwagę następujące wskazówki Blazor :</span><span class="sxs-lookup"><span data-stu-id="e9284-212">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="e9284-213">Wydziel dane na mniejsze fragmenty i Wyślij segmenty danych sekwencyjnie, dopóki wszystkie dane nie zostaną odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="e9284-213">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="e9284-214">Nie przydzielaj dużych obiektów w kodzie JavaScript i C#.</span><span class="sxs-lookup"><span data-stu-id="e9284-214">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="e9284-215">Nie blokuj głównego wątku interfejsu użytkownika przez długie okresy podczas wysyłania lub otrzymywania danych.</span><span class="sxs-lookup"><span data-stu-id="e9284-215">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="e9284-216">Zwolnij wszystkie używane pamięci, gdy proces zostanie ukończony lub anulowany.</span><span class="sxs-lookup"><span data-stu-id="e9284-216">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="e9284-217">Wymuś następujące dodatkowe wymagania dotyczące zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="e9284-217">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="e9284-218">Zadeklaruj maksymalny rozmiar pliku lub danych, który może zostać przesłany.</span><span class="sxs-lookup"><span data-stu-id="e9284-218">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="e9284-219">Zadeklaruj minimalną szybkość przekazywania z klienta na serwerze.</span><span class="sxs-lookup"><span data-stu-id="e9284-219">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="e9284-220">Po odebraniu danych przez serwer dane mogą być następujące:</span><span class="sxs-lookup"><span data-stu-id="e9284-220">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="e9284-221">Tymczasowo przechowywane w buforze pamięci do momentu zebrania wszystkich segmentów.</span><span class="sxs-lookup"><span data-stu-id="e9284-221">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="e9284-222">Wykorzystano natychmiast.</span><span class="sxs-lookup"><span data-stu-id="e9284-222">Consumed immediately.</span></span> <span data-ttu-id="e9284-223">Na przykład dane mogą być przechowywane bezpośrednio w bazie danych lub zapisywane na dysku w miarę odbierania poszczególnych segmentów.</span><span class="sxs-lookup"><span data-stu-id="e9284-223">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="e9284-224">Moduły JS</span><span class="sxs-lookup"><span data-stu-id="e9284-224">JS modules</span></span>

<span data-ttu-id="e9284-225">W przypadku izolacji JS program JS współdziała z domyślną obsługą języka [ECMAScript (](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) [model ECMAScript](https://tc39.es/ecma262/#sec-modules)) w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="e9284-225">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9284-226">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e9284-226">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="e9284-227">`InteropComponent.razor` przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałęzi wydania)</span><span class="sxs-lookup"><span data-stu-id="e9284-227">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
