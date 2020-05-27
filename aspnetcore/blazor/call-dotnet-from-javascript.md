---
<span data-ttu-id="6ecd0-101">title: "wywoływanie metod .NET z funkcji JavaScript w ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak wywoływać metody .NET z funkcji języka JavaScript w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-101">title: 'Call .NET methods from JavaScript functions in ASP.NET Core Blazor' author: description: 'Learn how to invoke .NET methods from JavaScript functions in Blazor apps.'</span></span>
<span data-ttu-id="6ecd0-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6ecd0-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6ecd0-103">'Blazor'</span></span>
- <span data-ttu-id="6ecd0-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6ecd0-104">'Identity'</span></span>
- <span data-ttu-id="6ecd0-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6ecd0-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6ecd0-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6ecd0-106">'Razor'</span></span>
- <span data-ttu-id="6ecd0-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-107">'SignalR' uid:</span></span> 

---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="6ecd0-108">Wywoływanie metod .NET z funkcji języka JavaScript w ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="6ecd0-108">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="6ecd0-109">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)i [Luke](https://github.com/guardrex) Latham</span><span class="sxs-lookup"><span data-stu-id="6ecd0-109">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6ecd0-110">BlazorAplikacja może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-110">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="6ecd0-111">Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie*js Interop*).</span><span class="sxs-lookup"><span data-stu-id="6ecd0-111">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="6ecd0-112">W tym artykule opisano wywoływanie metod .NET z języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-112">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="6ecd0-113">Aby uzyskać informacje na temat wywoływania funkcji JavaScript z platformy .NET, zobacz <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="6ecd0-113">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="6ecd0-114">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ecd0-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="6ecd0-115">Statyczne wywołanie metody .NET</span><span class="sxs-lookup"><span data-stu-id="6ecd0-115">Static .NET method call</span></span>

<span data-ttu-id="6ecd0-116">Aby wywołać statyczną metodę .NET z poziomu języka JavaScript, `DotNet.invokeMethod` Użyj `DotNet.invokeMethodAsync` funkcji or.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-116">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="6ecd0-117">Przekaż identyfikator metody statycznej, która ma być wywoływana, nazwę zestawu zawierającego funkcję i wszelkie argumenty.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-117">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="6ecd0-118">Wersja asynchroniczna jest preferowana do obsługi Blazor scenariuszy serwera.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-118">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="6ecd0-119">Metoda .NET musi być publiczna, statyczna i mieć [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atrybut.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-119">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="6ecd0-120">Wywoływanie otwartych metod ogólnych nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-120">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="6ecd0-121">Przykładowa aplikacja zawiera metodę języka C#, która zwraca `int` tablicę.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-121">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="6ecd0-122">Ten [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atrybut jest stosowany do metody.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-122">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="6ecd0-123">*Strony/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-123">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="6ecd0-124">Kod JavaScript obsługiwany przez klienta wywołuje metodę języka C# .NET.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-124">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="6ecd0-125">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-125">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="6ecd0-126">Gdy zostanie wybrany przycisk **ReturnArrayAsync Wyzwalaj metodę statyczną .NET** , sprawdź dane wyjściowe konsoli w narzędziach deweloperskich sieci Web w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-126">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="6ecd0-127">Dane wyjściowe konsoli są następujące:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-127">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="6ecd0-128">Czwarta wartość tablicy jest wypychana do tablicy ( `data.push(4);` ) zwracanej przez `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="6ecd0-128">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="6ecd0-129">Domyślnie identyfikator metody jest nazwą metody, ale można określić inny identyfikator przy użyciu [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) konstruktora atrybutu:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-129">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="6ecd0-130">W pliku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-130">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="6ecd0-131">Wywołanie metody wystąpienia</span><span class="sxs-lookup"><span data-stu-id="6ecd0-131">Instance method call</span></span>

<span data-ttu-id="6ecd0-132">Można również wywołać metody wystąpienia platformy .NET z poziomu języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-132">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="6ecd0-133">Aby wywołać metodę wystąpienia platformy .NET z poziomu języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-133">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="6ecd0-134">Przekaż wystąpienie platformy .NET przez odwołanie do języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-134">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="6ecd0-135">Wykonaj wywołanie statyczne do <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6ecd0-135">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="6ecd0-136">Zawiń wystąpienie w <xref:Microsoft.JSInterop.DotNetObjectReference> wystąpieniu i Wywołaj wystąpienie <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> <xref:Microsoft.JSInterop.DotNetObjectReference> .</span><span class="sxs-lookup"><span data-stu-id="6ecd0-136">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="6ecd0-137">Usuwanie <xref:Microsoft.JSInterop.DotNetObjectReference> obiektów (przykład pojawia się w dalszej części tej sekcji).</span><span class="sxs-lookup"><span data-stu-id="6ecd0-137">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="6ecd0-138">Wywołaj metody wystąpienia platformy .NET w wystąpieniu przy `invokeMethod` użyciu `invokeMethodAsync` funkcji lub.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-138">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="6ecd0-139">Wystąpienie programu .NET może być również przekazywać jako argument podczas wywoływania innych metod .NET z JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-139">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="6ecd0-140">Przykładowa aplikacja rejestruje komunikaty do konsoli po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-140">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="6ecd0-141">W poniższych przykładach zademonstrowanych przez przykładową aplikację można sprawdzić dane wyjściowe konsoli przeglądarki w narzędziach deweloperskich w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-141">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="6ecd0-142">Po wybraniu przycisku **Wyzwalaj metodę wystąpienia .NET HelloHelper. sayHello** jest `ExampleJsInterop.CallHelloHelperSayHello` wywoływana i przekazuje nazwę, `Blazor` do metody.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-142">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="6ecd0-143">*Strony/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-143">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="6ecd0-144">`CallHelloHelperSayHello`wywołuje funkcję JavaScript `sayHello` z nowym wystąpieniem `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="6ecd0-144">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="6ecd0-145">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-145">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="6ecd0-146">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-146">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="6ecd0-147">Nazwa jest przenoszona do `HelloHelper` konstruktora, który ustawia `HelloHelper.Name` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-147">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="6ecd0-148">Gdy funkcja JavaScript `sayHello` jest wykonywana, `HelloHelper.SayHello` zwraca `Hello, {Name}!` komunikat, który jest zapisywana w konsoli przez funkcję JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-148">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="6ecd0-149">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-149">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="6ecd0-150">Dane wyjściowe konsoli w narzędziach deweloperskich sieci Web w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-150">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="6ecd0-151">Aby uniknąć przecieków pamięci i zezwolić na wyrzucanie elementów bezużytecznych w składniku, który tworzy <xref:Microsoft.JSInterop.DotNetObjectReference> , należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-151">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="6ecd0-152">Usuń obiekt z klasy, która utworzyła <xref:Microsoft.JSInterop.DotNetObjectReference> wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-152">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="6ecd0-153">Poprzedni wzorzec przedstawiony w `ExampleJsInterop` klasie może być również zaimplementowany w składniku:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-153">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

* <span data-ttu-id="6ecd0-154">Gdy składnik lub Klasa nie usuwa <xref:Microsoft.JSInterop.DotNetObjectReference> metody Dispose z klienta przez wywołanie `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="6ecd0-154">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="6ecd0-155">Wywołanie metody wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="6ecd0-155">Component instance method call</span></span>

<span data-ttu-id="6ecd0-156">Aby wywołać metody .NET składnika:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-156">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="6ecd0-157">Użyj `invokeMethod` funkcji or, `invokeMethodAsync` Aby wykonać wywołanie metody statycznej do składnika.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-157">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="6ecd0-158">Metoda statyczna składnika zawija wywołanie do metody instancji jako wywołane <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="6ecd0-158">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

<span data-ttu-id="6ecd0-159">W języku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-159">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="6ecd0-160">*Strony/JSInteropComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-160">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="6ecd0-161">Jeśli istnieje kilka składników, z których każda wywołuje metody wystąpienia, użyj klasy pomocnika, aby wywołać metody wystąpienia (jako <xref:System.Action> s) każdego składnika.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-161">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as <xref:System.Action>s) of each component.</span></span>

<span data-ttu-id="6ecd0-162">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-162">In the following example:</span></span>

* <span data-ttu-id="6ecd0-163">`JSInteropExample`Składnik zawiera kilka `ListItem` składników.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-163">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="6ecd0-164">Każdy `ListItem` składnik składa się z komunikatu i przycisku.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-164">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="6ecd0-165">Po `ListItem` wybraniu przycisku składnik `ListItem` `UpdateMessage` Metoda zmienia tekst elementu listy i ukrywa przycisk.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-165">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="6ecd0-166">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-166">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="6ecd0-167">W języku JavaScript po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-167">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="6ecd0-168">*Shared/ListItem. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-168">*Shared/ListItem.razor*:</span></span>

```razor
@inject IJSRuntime JsRuntime

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
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
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

<span data-ttu-id="6ecd0-169">*Strony/JSInteropExample. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-169">*Pages/JSInteropExample.razor*:</span></span>

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

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="6ecd0-170">Unikaj cyklicznych odwołań do obiektów</span><span class="sxs-lookup"><span data-stu-id="6ecd0-170">Avoid circular object references</span></span>

<span data-ttu-id="6ecd0-171">Obiekty, które zawierają odwołania cykliczne, nie mogą być serializowane na kliencie dla obu:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-171">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="6ecd0-172">Wywołania metody .NET.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-172">.NET method calls.</span></span>
* <span data-ttu-id="6ecd0-173">Wywołania metody JavaScript z języka C#, gdy typem zwracanym są odwołania cykliczne.</span><span class="sxs-lookup"><span data-stu-id="6ecd0-173">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="6ecd0-174">Aby uzyskać więcej informacji, zobacz następujące problemy:</span><span class="sxs-lookup"><span data-stu-id="6ecd0-174">For more information, see the following issues:</span></span>

* [<span data-ttu-id="6ecd0-175">Odwołania cykliczne nie są obsługiwane, zrób dwa (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="6ecd0-175">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="6ecd0-176">Propozycja: Dodawanie mechanizmu do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/Runtime)</span><span class="sxs-lookup"><span data-stu-id="6ecd0-176">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="6ecd0-177">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6ecd0-177">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="6ecd0-178">InteropComponent. Razor — przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałąź wydania)</span><span class="sxs-lookup"><span data-stu-id="6ecd0-178">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="6ecd0-179">[Wykonywanie dużych transferów danych w Blazor aplikacjach serwera](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="6ecd0-179">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
