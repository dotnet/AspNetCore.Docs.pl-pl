---
title: 'Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Dowiedz się, jak wywoływać funkcje języka JavaScript z metod .NET w :::no-loc(Blazor)::: aplikacjach.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/20/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 5d9934c8bebbe994489380faf55140fce6beec95
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507801"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="b3b2c-103">Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="b3b2c-103">Call JavaScript functions from .NET methods in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="b3b2c-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)i [Luke](https://github.com/guardrex) Latham</span><span class="sxs-lookup"><span data-stu-id="b3b2c-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b3b2c-105">:::no-loc(Blazor):::Aplikacja może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="b3b2c-106">Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie *js Interop* ).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="b3b2c-107">W tym artykule opisano wywoływanie funkcji języka JavaScript z platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="b3b2c-108">Aby uzyskać informacje na temat wywoływania metod .NET w języku JavaScript, zobacz <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="b3b2c-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b3b2c-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b3b2c-110">Aby wywołać kod JavaScript z platformy .NET, użyj <xref:Microsoft.JSInterop.IJSRuntime> abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="b3b2c-111">Aby wystawić wywołania programu JS Interop, wstrzyknąć <xref:Microsoft.JSInterop.IJSRuntime> streszczenie w składniku.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="b3b2c-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> przyjmuje identyfikator funkcji języka JavaScript, która ma zostać wywołana wraz z dowolną liczbą argumentów do serializacji JSON.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="b3b2c-113">Identyfikator funkcji jest względny w stosunku do zakresu globalnego ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="b3b2c-114">Jeśli chcesz wywołać `window.someScope.someFunction` , identyfikator to `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="b3b2c-115">Nie ma potrzeby rejestrowania funkcji przed jej wywołaniem.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="b3b2c-116">Zwracanym typem `T` musi być również kod JSON możliwy do serializacji.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="b3b2c-117">`T` powinien być zgodny z typem .NET, który najlepiej jest mapowany do zwracanego typu JSON.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="b3b2c-118">Funkcje języka JavaScript, które zwracają [obietnicę](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , są wywoływane z <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="b3b2c-119">`InvokeAsync` odpakuje obietnicę i zwraca wartość oczekiwaną przez obietnicę.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="b3b2c-120">W przypadku :::no-loc(Blazor Server)::: aplikacji z włączoną funkcją prerenderowania Wywoływanie kodu JavaScript nie jest możliwe podczas początkowego wstępnego renderowania.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-120">For :::no-loc(Blazor Server)::: apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="b3b2c-121">Wywołania międzyoperacyjne języka JavaScript muszą zostać odroczone do momentu ustanowienia połączenia z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="b3b2c-122">Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy :::no-loc(Blazor Server)::: aplikacja jest prerenderowana](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-122">For more information, see the [Detect when a :::no-loc(Blazor Server)::: app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="b3b2c-123">Poniższy przykład jest oparty na [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="b3b2c-124">W przykładzie pokazano, jak wywołać funkcję języka JavaScript z metody języka C#, która odciąża wymóg od kodu dewelopera do istniejącego interfejsu API języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="b3b2c-125">Funkcja JavaScript akceptuje tablicę bajtową z metody C#, dekoduje tablicę i zwraca tekst do składnika do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="b3b2c-126">Wewnątrz `<head>` elementu `wwwroot/index.html` ( :::no-loc(Blazor WebAssembly)::: ) lub `Pages/_Host.cshtml` ( :::no-loc(Blazor Server)::: ), podaj funkcję języka JavaScript, która używa `TextDecoder` do dekodowania przekazaną tablicę i zwracają zdekodowaną wartość:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-126">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="b3b2c-127">Kod JavaScript, taki jak kod przedstawiony w powyższym przykładzie, można również załadować z pliku JavaScript ( `.js` ) z odwołaniem do pliku skryptu:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="b3b2c-128">Następujący składnik:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-128">The following component:</span></span>

* <span data-ttu-id="b3b2c-129">Wywołuje `convertArray` funkcję JavaScript przy użyciu `JS` przycisku składnika ( **`Convert Array`** ).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-129">Invokes the `convertArray` JavaScript function using `JS` when a component button ( **`Convert Array`** ) is selected.</span></span>
* <span data-ttu-id="b3b2c-130">Po wywołaniu funkcji języka JavaScript przenoszona tablica jest konwertowana na ciąg.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="b3b2c-131">Ciąg jest zwracany do składnika do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="b3b2c-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="b3b2c-132">IJSRuntime</span></span>

<span data-ttu-id="b3b2c-133">Aby użyć <xref:Microsoft.JSInterop.IJSRuntime> abstrakcji, należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="b3b2c-134">Wstrzyknąć <xref:Microsoft.JSInterop.IJSRuntime> streszczenie do :::no-loc(Razor)::: składnika ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the :::no-loc(Razor)::: component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="b3b2c-135">W `<head>` elemencie `wwwroot/index.html` ( :::no-loc(Blazor WebAssembly)::: ) lub `Pages/_Host.cshtml` ( :::no-loc(Blazor Server)::: ), podaj `handleTickerChanged` funkcję języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-135">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="b3b2c-136">Funkcja jest wywoływana z <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> i nie zwraca wartości:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="b3b2c-137">Wstrzyknąć <xref:Microsoft.JSInterop.IJSRuntime> streszczenie do klasy ( `.cs` ):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="b3b2c-138">W `<head>` elemencie `wwwroot/index.html` ( :::no-loc(Blazor WebAssembly)::: ) lub `Pages/_Host.cshtml` ( :::no-loc(Blazor Server)::: ), podaj `handleTickerChanged` funkcję języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-138">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="b3b2c-139">Funkcja jest wywoływana z `JS.InvokeAsync` i zwraca wartość:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-139">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="b3b2c-140">W przypadku generowania zawartości dynamicznej przy użyciu [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)należy użyć `[Inject]` atrybutu:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="b3b2c-141">W aplikacji przykładowej po stronie klienta, która jest dołączona do tego tematu, dostępne są dwie funkcje języka JavaScript, które współdziałają z modelem DOM, aby odbierać dane wejściowe użytkownika i wyświetlać komunikat powitalny:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="b3b2c-142">`showPrompt`: Generuje monit o zaakceptowanie danych wprowadzonych przez użytkownika (nazwę użytkownika) i zwraca nazwę obiektu wywołującego.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="b3b2c-143">`displayWelcome`: Przypisuje Komunikat powitalny od wywołującego do obiektu DOM z `id` `welcome` .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="b3b2c-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="b3b2c-145">Umieść `<script>` tag odwołujący się do pliku JavaScript w `wwwroot/index.html` pliku ( :::no-loc(Blazor WebAssembly)::: ) lub `Pages/_Host.cshtml` pliku ( :::no-loc(Blazor Server)::: ).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::).</span></span>

<span data-ttu-id="b3b2c-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span></span>

[!code-html[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="b3b2c-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span></span>

[!code-cshtml[](./common/samples/5.x/:::no-loc(Blazor):::ServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="b3b2c-148">Nie umieszczaj `<script>` znacznika w pliku składnika, ponieważ `<script>` nie można dynamicznie zaktualizować znacznika.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="b3b2c-149">Metody .NET współdziałają z funkcją JavaScript w `exampleJsInterop.js` pliku przez wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="b3b2c-150"><xref:Microsoft.JSInterop.IJSRuntime>Abstrakcja jest asynchroniczna, aby umożliwić obsługę :::no-loc(Blazor Server)::: scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="b3b2c-151">Jeśli aplikacja jest :::no-loc(Blazor WebAssembly)::: aplikacją i chcesz wywołać funkcję JavaScript synchronicznie, downcast do <xref:Microsoft.JSInterop.IJSInProcessRuntime> wywołania i <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> zamiast tego.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-151">If the app is a :::no-loc(Blazor WebAssembly)::: app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="b3b2c-152">Zalecamy, aby większość bibliotek międzyoperacyjnych JS używała asynchronicznych interfejsów API, aby upewnić się, że biblioteki są dostępne we wszystkich scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="b3b2c-153">Aby włączyć izolację JavaScript w standardowych [modułach języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), zobacz sekcję [ :::no-loc(Blazor)::: izolacja kodu JavaScript i odwołania do obiektów](#blazor-javascript-isolation-and-object-references) .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [:::no-loc(Blazor)::: JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="b3b2c-154">Przykładowa aplikacja zawiera składnik demonstrujący międzyoperacyjność JS.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="b3b2c-155">Składnik:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-155">The component:</span></span>

* <span data-ttu-id="b3b2c-156">Odbiera dane wprowadzane przez użytkownika za pośrednictwem wiersza polecenia języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="b3b2c-157">Zwraca tekst do składnika do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="b3b2c-158">Wywołuje drugą funkcję języka JavaScript, która współdziała z modelem DOM, aby wyświetlić komunikat powitalny.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="b3b2c-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-159">`Pages/JsInterop.razor`:</span></span>

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
                $"Hello {name}! Welcome to :::no-loc(Blazor):::!");
    }
}
```

<span data-ttu-id="b3b2c-160">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

1. <span data-ttu-id="b3b2c-161">Gdy `TriggerJsPrompt` jest wykonywane, wybierając **`Trigger JavaScript Prompt`** przycisk składnika, `showPrompt` Funkcja JavaScript dostępna w `wwwroot/exampleJsInterop.js` pliku jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="b3b2c-162">`showPrompt`Funkcja akceptuje dane wejściowe użytkownika (nazwę użytkownika), które są kodowane w formacie HTML i zwracane do składnika.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="b3b2c-163">Składnik przechowuje nazwę użytkownika w zmiennej lokalnej, `name` .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="b3b2c-164">Ciąg przechowywany w programie `name` jest zawarty w komunikacie powitalnym, który jest przesyłany do funkcji języka JavaScript, `displayWelcome` która renderuje Komunikat powitalny do znacznika nagłówka.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="b3b2c-165">Wywoływanie funkcji języka JavaScript typu void</span><span class="sxs-lookup"><span data-stu-id="b3b2c-165">Call a void JavaScript function</span></span>

<span data-ttu-id="b3b2c-166">Użyj <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> dla następujących:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="b3b2c-167">Funkcje języka JavaScript zwracające [wartość void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) lub [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="b3b2c-168">Jeśli program .NET nie jest wymagany do odczytu wyniku wywołania JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="b3b2c-169">Wykryj, kiedy :::no-loc(Blazor Server)::: aplikacja jest renderowana</span><span class="sxs-lookup"><span data-stu-id="b3b2c-169">Detect when a :::no-loc(Blazor Server)::: app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="b3b2c-170">Przechwyć odwołania do elementów</span><span class="sxs-lookup"><span data-stu-id="b3b2c-170">Capture references to elements</span></span>

<span data-ttu-id="b3b2c-171">Niektóre scenariusze międzyoperacyjności JS wymagają odwołań do elementów HTML.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="b3b2c-172">Na przykład Biblioteka interfejsu użytkownika może wymagać odwołania do elementu dla inicjalizacji lub może być konieczne wywołanie interfejsów API, takich jak `focus` lub `play` .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="b3b2c-173">Przechwyć odwołania do elementów HTML w składniku, korzystając z następującej metody:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="b3b2c-174">Dodaj `@ref` atrybut do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="b3b2c-175">Zdefiniuj pole typu, <xref:Microsoft.AspNetCore.Components.ElementReference> którego nazwa pasuje do wartości `@ref` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="b3b2c-176">Poniższy przykład pokazuje przechwytywanie odwołania do `username` `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="b3b2c-177">Użyj odwołania do elementu, aby zmodyfikować zawartość pustego elementu, który nie współdziała z :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with :::no-loc(Blazor):::.</span></span> <span data-ttu-id="b3b2c-178">Ten scenariusz jest przydatny, gdy interfejs API innej firmy dostarcza zawartość do elementu.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="b3b2c-179">Ponieważ :::no-loc(Blazor)::: nie współdziała z elementem, nie ma możliwości konfliktu między :::no-loc(Blazor)::: reprezentacją elementu a modelem dom.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-179">Because :::no-loc(Blazor)::: doesn't interact with the element, there's no possibility of a conflict between :::no-loc(Blazor):::'s representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="b3b2c-180">W poniższym przykładzie jest *niebezpieczne* do mutacji zawartości listy nieuporządkowanej ( `ul` ), ponieważ :::no-loc(Blazor)::: współdziała z modelem dom w celu wypełnienia elementów listy elementu ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because :::no-loc(Blazor)::: interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="b3b2c-181">Jeśli element JS Interop przyniesie zawartość elementu `MyList` i :::no-loc(Blazor)::: podejmuje próbę zastosowania różnic do elementu, różnice nie będą zgodne z modelem dom.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-181">If JS interop mutates the contents of element `MyList` and :::no-loc(Blazor)::: attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="b3b2c-182"><xref:Microsoft.AspNetCore.Components.ElementReference>Jest przenoszona przez kod JavaScript za pośrednictwem międzyoperacyjnego kodu js.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-182">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="b3b2c-183">Kod JavaScript odbiera `HTMLElement` wystąpienie, które może być używane z normalnymi interfejsami API modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-183">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="b3b2c-184">Na przykład poniższy kod definiuje metodę rozszerzenia .NET, która umożliwia wysłanie kliknięcia myszą do elementu:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-184">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="b3b2c-185">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-185">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="b3b2c-186">Użyj [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) w kodzie C#, aby skoncentrować się na elemencie, który jest wbudowanym :::no-loc(Blazor)::: strukturą i współpracuje z odwołaniami do elementów.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-186">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the :::no-loc(Blazor)::: framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="b3b2c-187">Aby wywołać funkcję języka JavaScript, która nie zwraca wartości, użyj <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b3b2c-188">Poniższy kod wyzwala zdarzenie po stronie klienta `Click` , wywołując poprzednią funkcję JavaScript z przechwyconą <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="b3b2c-188">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="b3b2c-189">Aby użyć metody rozszerzenia, Utwórz statyczną metodę rozszerzenia, która odbiera <xref:Microsoft.JSInterop.IJSRuntime> wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="b3b2c-190">`clickElement`Metoda jest wywoływana bezpośrednio dla obiektu.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-190">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="b3b2c-191">W poniższym przykładzie przyjęto założenie, że `TriggerClickEvent` Metoda jest dostępna z `JsInteropClasses` przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-191">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="b3b2c-192">`exampleButton`Zmienna jest wypełniana tylko po wyrenderowaniu składnika.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-192">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="b3b2c-193">W przypadku przekazanie niewypełnionego <xref:Microsoft.AspNetCore.Components.ElementReference> kodu JavaScript kod JavaScript otrzymuje wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="b3b2c-194">Aby manipulować odwołaniami do elementów po zakończeniu renderowania składnika, użyj [ `OnAfterRenderAsync` `OnAfterRender` metody lub cyklu życia składnika](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-194">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="b3b2c-195">Podczas pracy z typami ogólnymi i zwracają wartość, użyj <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="b3b2c-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="b3b2c-196">`GenericMethod` jest wywoływana bezpośrednio na obiekcie z typem.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="b3b2c-197">W poniższym przykładzie przyjęto założenie, że `GenericMethod` jest dostępny z `JsInteropClasses` przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="b3b2c-198">Elementy odniesienia między składnikami</span><span class="sxs-lookup"><span data-stu-id="b3b2c-198">Reference elements across components</span></span>

<span data-ttu-id="b3b2c-199"><xref:Microsoft.AspNetCore.Components.ElementReference>Nie można przekazywać między składnikami, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="b3b2c-200">Wystąpienie jest gwarantowane, że istnieje tylko po wyrenderowaniu składnika, który jest w trakcie lub po wykonaniu <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> metody składnika.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-200">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="b3b2c-201">Element <xref:Microsoft.AspNetCore.Components.ElementReference> to a [`struct`](/csharp/language-reference/builtin-types/struct) , którego nie można przesłać jako [parametru składnika](xref:blazor/components/index#component-parameters).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="b3b2c-202">Aby składnik nadrzędny mógł udostępnić odwołanie do elementu innym składnikom, składnik nadrzędny może:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-202">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="b3b2c-203">Zezwalaj składnikom podrzędnym na rejestrowanie wywołań zwrotnych.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-203">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="b3b2c-204">Wywołaj zarejestrowane wywołania zwrotne podczas <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> zdarzenia z odwołaniem do elementu.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-204">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="b3b2c-205">Pośrednio takie podejście umożliwia składnikom podrzędnym współdziałanie z odwołaniem do elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-205">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="b3b2c-206">Poniższy :::no-loc(Blazor WebAssembly)::: przykład ilustruje podejście.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-206">The following :::no-loc(Blazor WebAssembly)::: example illustrates the approach.</span></span>

<span data-ttu-id="b3b2c-207">W programie `<head>` z `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="b3b2c-207">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="b3b2c-208">W programie `<body>` z `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="b3b2c-208">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="b3b2c-209">`Pages/Index.razor` (składnik nadrzędny):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-209">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is :::no-loc(Blazor)::: working for you?" />
```

<span data-ttu-id="b3b2c-210">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-210">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="b3b2c-211">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-211">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="b3b2c-212">`Shared/SurveyPrompt.razor` (składnik podrzędny):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-212">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="b3b2c-213">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-213">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="b3b2c-214">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-214">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="b3b2c-215">Zabezpieczenia wywołań międzyoperacyjnych w ramach funkcjonalności JS</span><span class="sxs-lookup"><span data-stu-id="b3b2c-215">Harden JS interop calls</span></span>

<span data-ttu-id="b3b2c-216">Usługa JS Interop może zakończyć się niepowodzeniem z powodu błędów sieci i powinna być traktowana jako niezawodna.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-216">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="b3b2c-217">Domyślnie :::no-loc(Blazor Server)::: aplikacja przeprowadzi czas wywołań międzyoperacyjnych js na serwerze po jednej minucie.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-217">By default, a :::no-loc(Blazor Server)::: app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="b3b2c-218">Jeśli aplikacja może tolerować bardziej agresywny limit czasu, należy ustawić limit czasu przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-218">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="b3b2c-219">Globalnie w programie `Startup.ConfigureServices` Określ limit czasu:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-219">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSide:::no-loc(Blazor):::(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="b3b2c-220">Dla wywołania w kodzie składnika pojedyncze wywołanie może określać limit czasu:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-220">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="b3b2c-221">Więcej informacji o wyczerpaniu zasobów znajduje się w temacie <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-221">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="b3b2c-222">Unikaj cyklicznych odwołań do obiektów</span><span class="sxs-lookup"><span data-stu-id="b3b2c-222">Avoid circular object references</span></span>

<span data-ttu-id="b3b2c-223">Obiekty, które zawierają odwołania cykliczne, nie mogą być serializowane na kliencie dla obu:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-223">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="b3b2c-224">Wywołania metody .NET.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-224">.NET method calls.</span></span>
* <span data-ttu-id="b3b2c-225">Wywołania metody JavaScript z języka C#, gdy typem zwracanym są odwołania cykliczne.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-225">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="b3b2c-226">Aby uzyskać więcej informacji, zobacz następujące problemy:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-226">For more information, see the following issues:</span></span>

* [<span data-ttu-id="b3b2c-227">Odwołania cykliczne nie są obsługiwane, zrób dwa (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="b3b2c-227">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="b3b2c-228">Propozycja: Dodawanie mechanizmu do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/Runtime)</span><span class="sxs-lookup"><span data-stu-id="b3b2c-228">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="b3b2c-229">:::no-loc(Blazor)::: Izolacja kodu JavaScript i odwołania do obiektów</span><span class="sxs-lookup"><span data-stu-id="b3b2c-229">:::no-loc(Blazor)::: JavaScript isolation and object references</span></span>

<span data-ttu-id="b3b2c-230">:::no-loc(Blazor)::: Włącza izolację JavaScript w standardowych [modułach języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-230">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="b3b2c-231">Izolacja JavaScript zapewnia następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-231">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="b3b2c-232">Zaimportowana wartość JavaScript nie jest już zanieczyszczana globalną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-232">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="b3b2c-233">Odbiorcy biblioteki i składników nie są zobowiązani do zaimportowania powiązanego języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-233">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="b3b2c-234">Na przykład poniższy moduł JavaScript eksportuje funkcję JavaScript do wyświetlania monitu przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-234">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="b3b2c-235">Dodaj poprzedni moduł JavaScript do biblioteki .NET jako statyczny element zawartości sieci Web ( `wwwroot/exampleJsInterop.js` ), a następnie zaimportuj moduł do kodu platformy .NET przy użyciu <xref:Microsoft.JSInterop.IJSRuntime> usługi.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-235">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="b3b2c-236">Usługa jest wstrzykiwana jako `js` (niepokazywana) dla następującego przykładu:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-236">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="b3b2c-237">`import`Identyfikator w poprzednim przykładzie jest specjalnym identyfikatorem używanym specjalnie do importowania modułu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-237">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="b3b2c-238">Określ moduł przy użyciu jego stabilnej statycznej ścieżki zasobów sieci Web: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-238">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="b3b2c-239">Symbol zastępczy `{LIBRARY NAME}` jest nazwą biblioteki.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-239">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="b3b2c-240">Symbol zastępczy `{PATH UNDER WWWROOT}` jest ścieżką do skryptu w sekcji `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-240">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="b3b2c-241"><xref:Microsoft.JSInterop.IJSRuntime> Importuje moduł jako `IJSObjectReference` , który reprezentuje odwołanie do obiektu JavaScript z kodu platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-241"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="b3b2c-242">Użyj `IJSObjectReference` do wywołania wyeksportowanych funkcji języka JavaScript z modułu:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-242">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="b3b2c-243">`IJSInProcessObjectReference` reprezentuje odwołanie do obiektu JavaScript, którego funkcje mogą być wywoływane synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-243">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="b3b2c-244">`IJSUnmarshalledObjectReference` reprezentuje odwołanie do obiektu JavaScript, którego funkcje mogą być wywoływane bez nakładu na Serializowanie danych platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-244">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="b3b2c-245">Może to być stosowane w :::no-loc(Blazor WebAssembly)::: przypadku, gdy wydajność jest istotna:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-245">This can be used in :::no-loc(Blazor WebAssembly)::: when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = :::no-loc(Blazor):::.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)js;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

<span data-ttu-id="b3b2c-246">W poprzednim przykładzie <xref:Microsoft.JSInterop.IJSRuntime> Usługa jest wstrzykiwana do klasy i przypisana do `js` (nie jest wyświetlana).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-246">In the preceding example, the <xref:Microsoft.JSInterop.IJSRuntime> service is injected into the class and assigned to `js` (not shown).</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="b3b2c-247">Korzystanie z bibliotek języka JavaScript, które renderują interfejs użytkownika (elementy DOM)</span><span class="sxs-lookup"><span data-stu-id="b3b2c-247">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="b3b2c-248">Czasami warto używać bibliotek języka JavaScript, które tworzą widoczne elementy interfejsu użytkownika w modelu DOM przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-248">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="b3b2c-249">Na pierwszy rzut oka może wydawać się trudne, ponieważ :::no-loc(Blazor)::: system różnicowania polega na kontroli nad drzewem elementów Dom i uruchamianiu do błędów, jeśli jakiś kod zewnętrzny przydzieli drzewo Dom i unieważnia swój mechanizm do zastosowania różnic.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-249">At first glance, this might seem difficult because :::no-loc(Blazor):::'s diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="b3b2c-250">Nie jest to :::no-loc(Blazor)::: specyficzne ograniczenie.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-250">This isn't a :::no-loc(Blazor):::-specific limitation.</span></span> <span data-ttu-id="b3b2c-251">To samo wyzwanie występuje w przypadku wszystkich opartych na różnicach struktur interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-251">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="b3b2c-252">Na szczęście proste osadzanie wygenerowanego zewnętrznie interfejsu użytkownika w :::no-loc(Blazor)::: interfejsie użytkownika składnika jest niezawodne.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-252">Fortunately, it's straightforward to embed externally-generated UI within a :::no-loc(Blazor)::: component UI reliably.</span></span> <span data-ttu-id="b3b2c-253">Zalecaną techniką jest `.razor` utworzenie pustego elementu przez kod składnika (plik).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-253">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="b3b2c-254">W odniesieniu do :::no-loc(Blazor)::: systemu różnicowego, element jest zawsze pusty, dlatego moduł renderowania nie odnosi się do elementu i zamiast niego opuszcza jego zawartość.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-254">As far as :::no-loc(Blazor):::'s diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="b3b2c-255">Dzięki temu można bezpiecznie wypełnić element dowolną zewnętrznie zarządzaną zawartością.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-255">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="b3b2c-256">Poniższy przykład demonstruje koncepcję.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-256">The following example demonstrates the concept.</span></span> <span data-ttu-id="b3b2c-257">W `if` instrukcji when `firstRender` `true` , zrób coś z `myElement` .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-257">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="b3b2c-258">Na przykład Wywołaj zewnętrzną bibliotekę JavaScript w celu jej wypełnienia.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-258">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="b3b2c-259">:::no-loc(Blazor)::: pozostawia zawartość elementu tylko do momentu usunięcia tego składnika.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-259">:::no-loc(Blazor)::: leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="b3b2c-260">Po usunięciu składnika zostanie również usunięta cała poddrzewo DOM składnika.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-260">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a :::no-loc(Blazor)::: component rendered at @DateTime.Now</h1>

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

<span data-ttu-id="b3b2c-261">Jako bardziej szczegółowy przykład rozważmy poniższy składnik, który renderuje interaktywną mapę przy użyciu [interfejsów API MapBox typu open source](https://www.mapbox.com/):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-261">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="b3b2c-262">Odpowiedni moduł JavaScript, który powinien być umieszczony w `wwwroot/mapComponent.js` , jest następujący:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-262">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="b3b2c-263">W poprzednim przykładzie Zastąp ciąg `{ACCESS TOKEN}` prawidłowym tokenem dostępu, z którego można uzyskać dostęp https://account.mapbox.com .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-263">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="b3b2c-264">Aby wygenerować poprawne style, Dodaj następujący tag arkusza stylów do strony HTML hosta ( `index.html` lub `_Host.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-264">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="b3b2c-265">W powyższym przykładzie jest tworzony interaktywny interfejs użytkownika mapy, w którym użytkownik:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-265">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="b3b2c-266">Można przeciągnąć, aby przewinąć lub powiększyć.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-266">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="b3b2c-267">Kliknij przycisk przyciski, aby przejść do wstępnie zdefiniowanych lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-267">Click buttons to jump to predefined locations.</span></span>

![Mapa MapBox ulica Tokio, Japonia z przyciskami do wyboru Bristol, Zjednoczone Królestwo i Tokio, Japonia](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="b3b2c-269">Najważniejsze kwestie, które należy zrozumieć:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-269">The key points to understand are:</span></span>

 * <span data-ttu-id="b3b2c-270">Wartość `<div>` with `@ref="mapElement"` ma wartość pustą :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-270">The `<div>` with `@ref="mapElement"` is left empty as far as :::no-loc(Blazor)::: is concerned.</span></span> <span data-ttu-id="b3b2c-271">Z tego względu `mapbox-gl.js` można bezpiecznie wypełnić i zmodyfikować jego zawartość w czasie.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-271">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="b3b2c-272">Tej techniki można użyć z dowolną biblioteką języka JavaScript, która renderuje interfejs użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-272">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="b3b2c-273">Można nawet osadzić składniki z struktury SPA w języku JavaScript innej firmy w :::no-loc(Blazor)::: składnikach programu, o ile nie spróbują się dowiedzieć i modyfikować innych części strony.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-273">You could even embed components from a third-party JavaScript SPA framework inside :::no-loc(Blazor)::: components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="b3b2c-274">*Nie* jest bezpieczny dla zewnętrznego kodu JavaScript, aby modyfikować elementy, które nie są :::no-loc(Blazor)::: traktowane jako puste.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-274">It is *not* safe for external JavaScript code to modify elements that :::no-loc(Blazor)::: does not regard as empty.</span></span>
 * <span data-ttu-id="b3b2c-275">Korzystając z tego podejścia, należy wziąć pod uwagę reguły dotyczące sposobu :::no-loc(Blazor)::: zachowywania lub niszczenia elementów dom.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-275">When using this approach, bear in mind the rules about how :::no-loc(Blazor)::: retains or destroys DOM elements.</span></span> <span data-ttu-id="b3b2c-276">W poprzednim przykładzie składnik bezpiecznie obsługuje przycisk kliknij zdarzenia i aktualizuje istniejące wystąpienie mapy, ponieważ elementy DOM są zachowywane w miarę możliwości domyślnie.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-276">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="b3b2c-277">Jeśli renderuje listę elementów mapy z wewnątrz `@foreach` pętli, chcesz użyć, `@key` Aby zapewnić zachowywanie wystąpień składników.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-277">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="b3b2c-278">W przeciwnym razie zmiany w danych listy mogą spowodować wystąpienie składników, aby zachować stan poprzednich wystąpień w niepożądany sposób.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-278">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="b3b2c-279">Aby uzyskać więcej informacji, zobacz [Używanie @key do zachowywania elementów i składników](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-279">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="b3b2c-280">Ponadto w poprzednim przykładzie pokazano, jak można hermetyzować logikę JavaScript i zależności w module ES6 i ładować ją dynamicznie przy użyciu `import` identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-280">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="b3b2c-281">Aby uzyskać więcej informacji, zobacz temat [izolacja JavaScript i odwołania do obiektów](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-281">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="b3b2c-282">Limity rozmiaru dla wywołań międzyoperacyjnych w JS</span><span class="sxs-lookup"><span data-stu-id="b3b2c-282">Size limits on JS interop calls</span></span>

<span data-ttu-id="b3b2c-283">W programie :::no-loc(Blazor WebAssembly)::: platforma nie nakłada ograniczeń dotyczących rozmiaru danych wejściowych i wyjściowych wywołań js międzyoperacyjnych.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-283">In :::no-loc(Blazor WebAssembly):::, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="b3b2c-284">W programie :::no-loc(Blazor Server)::: wynik wywołania elementu js Interop jest ograniczony przez maksymalny rozmiar ładunku wymuszony przez :::no-loc(SignalR)::: ( <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> ), który domyślnie 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-284">In :::no-loc(Blazor Server):::, the result of a JS interop call is limited by the maximum payload size enforced by :::no-loc(SignalR)::: (<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="b3b2c-285">Aplikacje, które próbują odpowiedzieć na wywołanie elementu JS Interop z ładunkiem większym niż <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> Zgłoś błąd.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-285">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="b3b2c-286">Większy limit można skonfigurować przez modyfikację <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-286">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="b3b2c-287">W poniższym przykładzie ustawiono maksymalny rozmiar komunikatu odbioru na 64 KB (64 \* 1024 \* 1024):</span><span class="sxs-lookup"><span data-stu-id="b3b2c-287">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSide:::no-loc(Blazor):::()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="b3b2c-288">Zwiększenie :::no-loc(SignalR)::: limitu jest kosztem wymaganym przez użycie większej liczby zasobów serwera i ujawnia serwerowi zwiększone ryzyko ze strony złośliwego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-288">Increasing the :::no-loc(SignalR)::: limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="b3b2c-289">Ponadto odczytywanie dużej ilości zawartości w usłudze do pamięci jako ciągów lub tablic bajtowych może również spowodować, że alokacje działające nieprawidłowo z modułem wyrzucania elementów bezużytecznych, co skutkuje dodatkowymi karami za wydajność.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-289">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="b3b2c-290">Jedną z opcji odczytu dużych ładunków jest rozważenie wysłania zawartości w mniejszych fragmentach i przetworzenie ładunku jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="b3b2c-290">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="b3b2c-291">Można go użyć podczas odczytywania dużych ładunków JSON lub w przypadku, gdy dane są dostępne w języku JavaScript jako nieprzetworzone bajty.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-291">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="b3b2c-292">Przykład demonstrujący wysyłanie dużych ładunków binarnych w programie :::no-loc(Blazor Server)::: , które używają technik podobnych do `InputFile` składnika, można znaleźć w [przykładowej aplikacji przesyłania danych binarnych](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="b3b2c-292">For an example that demonstrates sending large binary payloads in :::no-loc(Blazor Server)::: that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="b3b2c-293">Podczas opracowywania kodu, który przesyła dużą ilość danych między językami JavaScript i należy wziąć pod uwagę następujące wskazówki :::no-loc(Blazor)::: :</span><span class="sxs-lookup"><span data-stu-id="b3b2c-293">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and :::no-loc(Blazor)::::</span></span>

* <span data-ttu-id="b3b2c-294">Wydziel dane na mniejsze fragmenty i Wyślij segmenty danych sekwencyjnie, dopóki wszystkie dane nie zostaną odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-294">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="b3b2c-295">Nie przydzielaj dużych obiektów w kodzie JavaScript i C#.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-295">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="b3b2c-296">Nie blokuj głównego wątku interfejsu użytkownika przez długie okresy podczas wysyłania lub otrzymywania danych.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-296">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="b3b2c-297">Zwolnij wszystkie używane pamięci, gdy proces zostanie ukończony lub anulowany.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-297">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="b3b2c-298">Wymuś następujące dodatkowe wymagania dotyczące zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-298">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="b3b2c-299">Zadeklaruj maksymalny rozmiar pliku lub danych, który może zostać przesłany.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-299">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="b3b2c-300">Zadeklaruj minimalną szybkość przekazywania z klienta na serwerze.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-300">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="b3b2c-301">Po odebraniu danych przez serwer dane mogą być następujące:</span><span class="sxs-lookup"><span data-stu-id="b3b2c-301">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="b3b2c-302">Tymczasowo przechowywane w buforze pamięci do momentu zebrania wszystkich segmentów.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-302">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="b3b2c-303">Wykorzystano natychmiast.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-303">Consumed immediately.</span></span> <span data-ttu-id="b3b2c-304">Na przykład dane mogą być przechowywane bezpośrednio w bazie danych lub zapisywane na dysku w miarę odbierania poszczególnych segmentów.</span><span class="sxs-lookup"><span data-stu-id="b3b2c-304">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3b2c-305">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b3b2c-305">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="b3b2c-306">InteropComponent. Razor — przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałąź wydania)</span><span class="sxs-lookup"><span data-stu-id="b3b2c-306">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
