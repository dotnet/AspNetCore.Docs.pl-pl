---
title: Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak wywoływać funkcje języka JavaScript z metod .NET w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 5b28ad594567e7bfc87e15eed419bea520125654
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280300"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="92ada-103">Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="92ada-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="92ada-104">BlazorAplikacja może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-104">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="92ada-105">Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie *js Interop*).</span><span class="sxs-lookup"><span data-stu-id="92ada-105">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="92ada-106">W tym artykule opisano wywoływanie funkcji języka JavaScript z platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="92ada-106">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="92ada-107">Aby uzyskać informacje na temat wywoływania metod .NET w języku JavaScript, zobacz <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="92ada-107">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="92ada-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="92ada-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="92ada-109">Dodaj pliki JS ( `<script>` tagi) przed tagiem zamykającym `</body>` w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="92ada-109">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="92ada-110">Upewnij się, że pliki JS z metodami międzyoperacyjnymi JS są uwzględnione przed Blazor plikami Framework js.</span><span class="sxs-lookup"><span data-stu-id="92ada-110">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

<span data-ttu-id="92ada-111">Aby wywołać kod JavaScript z platformy .NET, użyj <xref:Microsoft.JSInterop.IJSRuntime> abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="92ada-111">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="92ada-112">Aby wystawić wywołania programu JS Interop, wstrzyknąć <xref:Microsoft.JSInterop.IJSRuntime> streszczenie w składniku.</span><span class="sxs-lookup"><span data-stu-id="92ada-112">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="92ada-113"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> przyjmuje identyfikator funkcji języka JavaScript, która ma zostać wywołana wraz z dowolną liczbą argumentów do serializacji JSON.</span><span class="sxs-lookup"><span data-stu-id="92ada-113"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="92ada-114">Identyfikator funkcji jest względny w stosunku do zakresu globalnego ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="92ada-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="92ada-115">Jeśli chcesz wywołać `window.someScope.someFunction` , identyfikator to `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="92ada-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="92ada-116">Nie ma potrzeby rejestrowania funkcji przed jej wywołaniem.</span><span class="sxs-lookup"><span data-stu-id="92ada-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="92ada-117">Zwracanym typem `T` musi być również kod JSON możliwy do serializacji.</span><span class="sxs-lookup"><span data-stu-id="92ada-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="92ada-118">`T` powinien być zgodny z typem .NET, który najlepiej jest mapowany do zwracanego typu JSON.</span><span class="sxs-lookup"><span data-stu-id="92ada-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="92ada-119">Funkcje języka JavaScript, które zwracają [obietnicę](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , są wywoływane z <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="92ada-119">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="92ada-120">`InvokeAsync` odpakuje obietnicę i zwraca wartość oczekiwaną przez obietnicę.</span><span class="sxs-lookup"><span data-stu-id="92ada-120">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="92ada-121">W przypadku Blazor Server aplikacji z włączoną funkcją prerenderowania Wywoływanie kodu JavaScript nie jest możliwe podczas początkowego wstępnego renderowania.</span><span class="sxs-lookup"><span data-stu-id="92ada-121">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="92ada-122">Wywołania międzyoperacyjne języka JavaScript muszą zostać odroczone do momentu ustanowienia połączenia z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="92ada-122">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="92ada-123">Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy Blazor Server aplikacja jest prerenderowana](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="92ada-123">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="92ada-124">Poniższy przykład jest oparty na [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-124">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="92ada-125">W przykładzie pokazano, jak wywołać funkcję języka JavaScript z metody języka C#, która odciąża wymóg od kodu dewelopera do istniejącego interfejsu API języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-125">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="92ada-126">Funkcja JavaScript akceptuje tablicę bajtową z metody C#, dekoduje tablicę i zwraca tekst do składnika do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="92ada-126">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="92ada-127">Wewnątrz `<head>` elementu `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ), podaj funkcję języka JavaScript, która używa `TextDecoder` do dekodowania przekazaną tablicę i zwracają zdekodowaną wartość:</span><span class="sxs-lookup"><span data-stu-id="92ada-127">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="92ada-128">Kod JavaScript, taki jak kod przedstawiony w powyższym przykładzie, można również załadować z pliku JavaScript ( `.js` ) z odwołaniem do pliku skryptu:</span><span class="sxs-lookup"><span data-stu-id="92ada-128">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="92ada-129">Następujący składnik:</span><span class="sxs-lookup"><span data-stu-id="92ada-129">The following component:</span></span>

* <span data-ttu-id="92ada-130">Wywołuje `convertArray` funkcję JavaScript przy użyciu `JS` przycisku składnika ( **`Convert Array`** ).</span><span class="sxs-lookup"><span data-stu-id="92ada-130">Invokes the `convertArray` JavaScript function using `JS` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="92ada-131">Po wywołaniu funkcji języka JavaScript przenoszona tablica jest konwertowana na ciąg.</span><span class="sxs-lookup"><span data-stu-id="92ada-131">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="92ada-132">Ciąg jest zwracany do składnika do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="92ada-132">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="92ada-133">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="92ada-133">IJSRuntime</span></span>

<span data-ttu-id="92ada-134">Aby użyć <xref:Microsoft.JSInterop.IJSRuntime> abstrakcji, należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="92ada-134">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="92ada-135">Wstrzyknąć <xref:Microsoft.JSInterop.IJSRuntime> streszczenie do Razor składnika ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="92ada-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="92ada-136">W `<head>` elemencie `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ), podaj `handleTickerChanged` funkcję języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="92ada-137">Funkcja jest wywoływana z <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> i nie zwraca wartości:</span><span class="sxs-lookup"><span data-stu-id="92ada-137">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="92ada-138">Wstrzyknąć <xref:Microsoft.JSInterop.IJSRuntime> streszczenie do klasy ( `.cs` ):</span><span class="sxs-lookup"><span data-stu-id="92ada-138">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="92ada-139">W `<head>` elemencie `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ), podaj `handleTickerChanged` funkcję języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-139">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="92ada-140">Funkcja jest wywoływana z `JS.InvokeAsync` i zwraca wartość:</span><span class="sxs-lookup"><span data-stu-id="92ada-140">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="92ada-141">W przypadku generowania zawartości dynamicznej przy użyciu [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)należy użyć `[Inject]` atrybutu:</span><span class="sxs-lookup"><span data-stu-id="92ada-141">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="92ada-142">W aplikacji przykładowej po stronie klienta, która jest dołączona do tego tematu, dostępne są dwie funkcje języka JavaScript, które współdziałają z modelem DOM, aby odbierać dane wejściowe użytkownika i wyświetlać komunikat powitalny:</span><span class="sxs-lookup"><span data-stu-id="92ada-142">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="92ada-143">`showPrompt`: Generuje monit o zaakceptowanie danych wprowadzonych przez użytkownika (nazwę użytkownika) i zwraca nazwę obiektu wywołującego.</span><span class="sxs-lookup"><span data-stu-id="92ada-143">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="92ada-144">`displayWelcome`: Przypisuje Komunikat powitalny od wywołującego do obiektu DOM z `id` `welcome` .</span><span class="sxs-lookup"><span data-stu-id="92ada-144">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="92ada-145">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="92ada-145">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="92ada-146">Umieść `<script>` tag odwołujący się do pliku JavaScript w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="92ada-146">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="92ada-147">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="92ada-147">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=23)]

<span data-ttu-id="92ada-148">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="92ada-148">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="92ada-149">Nie umieszczaj `<script>` znacznika w pliku składnika, ponieważ `<script>` nie można dynamicznie zaktualizować znacznika.</span><span class="sxs-lookup"><span data-stu-id="92ada-149">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="92ada-150">Metody .NET współdziałają z funkcją JavaScript w `exampleJsInterop.js` pliku przez wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="92ada-150">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="92ada-151"><xref:Microsoft.JSInterop.IJSRuntime>Abstrakcja jest asynchroniczna, aby umożliwić obsługę Blazor Server scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="92ada-151">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="92ada-152">Jeśli aplikacja jest Blazor WebAssembly aplikacją i chcesz wywołać funkcję JavaScript synchronicznie, downcast do <xref:Microsoft.JSInterop.IJSInProcessRuntime> wywołania i <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> zamiast tego.</span><span class="sxs-lookup"><span data-stu-id="92ada-152">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="92ada-153">Zalecamy, aby większość bibliotek międzyoperacyjnych JS używała asynchronicznych interfejsów API, aby upewnić się, że biblioteki są dostępne we wszystkich scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="92ada-153">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="92ada-154">Aby włączyć izolację JavaScript w standardowych [modułach języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), zobacz sekcję [ Blazor izolacja kodu JavaScript i odwołania do obiektów](#blazor-javascript-isolation-and-object-references) .</span><span class="sxs-lookup"><span data-stu-id="92ada-154">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="92ada-155">Przykładowa aplikacja zawiera składnik demonstrujący międzyoperacyjność JS.</span><span class="sxs-lookup"><span data-stu-id="92ada-155">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="92ada-156">Składnik:</span><span class="sxs-lookup"><span data-stu-id="92ada-156">The component:</span></span>

* <span data-ttu-id="92ada-157">Odbiera dane wprowadzane przez użytkownika za pośrednictwem wiersza polecenia języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-157">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="92ada-158">Zwraca tekst do składnika do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="92ada-158">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="92ada-159">Wywołuje drugą funkcję języka JavaScript, która współdziała z modelem DOM, aby wyświetlić komunikat powitalny.</span><span class="sxs-lookup"><span data-stu-id="92ada-159">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="92ada-160">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="92ada-160">`Pages/JsInterop.razor`:</span></span>

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
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="92ada-161">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="92ada-161">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="92ada-162">Gdy `TriggerJsPrompt` jest wykonywane, wybierając **`Trigger JavaScript Prompt`** przycisk składnika, `showPrompt` Funkcja JavaScript dostępna w `wwwroot/exampleJsInterop.js` pliku jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="92ada-162">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="92ada-163">`showPrompt`Funkcja akceptuje dane wejściowe użytkownika (nazwę użytkownika), które są kodowane w formacie HTML i zwracane do składnika.</span><span class="sxs-lookup"><span data-stu-id="92ada-163">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="92ada-164">Składnik przechowuje nazwę użytkownika w zmiennej lokalnej, `name` .</span><span class="sxs-lookup"><span data-stu-id="92ada-164">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="92ada-165">Ciąg przechowywany w programie `name` jest zawarty w komunikacie powitalnym, który jest przesyłany do funkcji języka JavaScript, `displayWelcome` która renderuje Komunikat powitalny do znacznika nagłówka.</span><span class="sxs-lookup"><span data-stu-id="92ada-165">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="92ada-166">Wywoływanie funkcji języka JavaScript typu void</span><span class="sxs-lookup"><span data-stu-id="92ada-166">Call a void JavaScript function</span></span>

<span data-ttu-id="92ada-167">Użyj <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> dla następujących:</span><span class="sxs-lookup"><span data-stu-id="92ada-167">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="92ada-168">Funkcje języka JavaScript zwracające [wartość void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) lub [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span><span class="sxs-lookup"><span data-stu-id="92ada-168">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="92ada-169">Jeśli program .NET nie jest wymagany do odczytu wyniku wywołania JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-169">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="92ada-170">Wykryj, kiedy Blazor Server aplikacja jest renderowana</span><span class="sxs-lookup"><span data-stu-id="92ada-170">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="92ada-171">Przechwyć odwołania do elementów</span><span class="sxs-lookup"><span data-stu-id="92ada-171">Capture references to elements</span></span>

<span data-ttu-id="92ada-172">Niektóre scenariusze międzyoperacyjności JS wymagają odwołań do elementów HTML.</span><span class="sxs-lookup"><span data-stu-id="92ada-172">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="92ada-173">Na przykład Biblioteka interfejsu użytkownika może wymagać odwołania do elementu dla inicjalizacji lub może być konieczne wywołanie interfejsów API, takich jak `focus` lub `play` .</span><span class="sxs-lookup"><span data-stu-id="92ada-173">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="92ada-174">Przechwyć odwołania do elementów HTML w składniku, korzystając z następującej metody:</span><span class="sxs-lookup"><span data-stu-id="92ada-174">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="92ada-175">Dodaj `@ref` atrybut do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="92ada-175">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="92ada-176">Zdefiniuj pole typu, <xref:Microsoft.AspNetCore.Components.ElementReference> którego nazwa pasuje do wartości `@ref` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="92ada-176">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="92ada-177">Poniższy przykład pokazuje przechwytywanie odwołania do `username` `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="92ada-177">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="92ada-178">Użyj odwołania do elementu, aby zmodyfikować zawartość pustego elementu, który nie współdziała z Blazor .</span><span class="sxs-lookup"><span data-stu-id="92ada-178">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="92ada-179">Ten scenariusz jest przydatny, gdy interfejs API innej firmy dostarcza zawartość do elementu.</span><span class="sxs-lookup"><span data-stu-id="92ada-179">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="92ada-180">Ponieważ Blazor nie współdziała z elementem, nie ma możliwości konfliktu między Blazor reprezentacją elementu a modelem dom.</span><span class="sxs-lookup"><span data-stu-id="92ada-180">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="92ada-181">W poniższym przykładzie jest *niebezpieczne* do mutacji zawartości listy nieuporządkowanej ( `ul` ), ponieważ Blazor współdziała z modelem dom w celu wypełnienia elementów listy elementu ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="92ada-181">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="92ada-182">Jeśli element JS Interop przyniesie zawartość elementu `MyList` i Blazor podejmuje próbę zastosowania różnic do elementu, różnice nie będą zgodne z modelem dom.</span><span class="sxs-lookup"><span data-stu-id="92ada-182">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="92ada-183"><xref:Microsoft.AspNetCore.Components.ElementReference>Jest przenoszona przez kod JavaScript za pośrednictwem międzyoperacyjnego kodu js.</span><span class="sxs-lookup"><span data-stu-id="92ada-183">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="92ada-184">Kod JavaScript odbiera `HTMLElement` wystąpienie, które może być używane z normalnymi interfejsami API modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="92ada-184">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="92ada-185">Na przykład poniższy kod definiuje metodę rozszerzenia .NET, która umożliwia wysłanie kliknięcia myszą do elementu:</span><span class="sxs-lookup"><span data-stu-id="92ada-185">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="92ada-186">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="92ada-186">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="92ada-187">Użyj [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) w kodzie C#, aby skoncentrować się na elemencie, który jest wbudowanym Blazor strukturą i współpracuje z odwołaniami do elementów.</span><span class="sxs-lookup"><span data-stu-id="92ada-187">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="92ada-188">Aby wywołać funkcję języka JavaScript, która nie zwraca wartości, użyj <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="92ada-188">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="92ada-189">Poniższy kod wyzwala zdarzenie po stronie klienta `Click` , wywołując poprzednią funkcję JavaScript z przechwyconą <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="92ada-189">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="92ada-190">Aby użyć metody rozszerzenia, Utwórz statyczną metodę rozszerzenia, która odbiera <xref:Microsoft.JSInterop.IJSRuntime> wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="92ada-190">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="92ada-191">`clickElement`Metoda jest wywoływana bezpośrednio dla obiektu.</span><span class="sxs-lookup"><span data-stu-id="92ada-191">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="92ada-192">W poniższym przykładzie przyjęto założenie, że `TriggerClickEvent` Metoda jest dostępna z `JsInteropClasses` przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="92ada-192">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="92ada-193">`exampleButton`Zmienna jest wypełniana tylko po wyrenderowaniu składnika.</span><span class="sxs-lookup"><span data-stu-id="92ada-193">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="92ada-194">W przypadku przekazanie niewypełnionego <xref:Microsoft.AspNetCore.Components.ElementReference> kodu JavaScript kod JavaScript otrzymuje wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="92ada-194">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="92ada-195">Aby manipulować odwołaniami do elementów po zakończeniu renderowania składnika, użyj [ `OnAfterRenderAsync` `OnAfterRender` metody lub cyklu życia składnika](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="92ada-195">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="92ada-196">Podczas pracy z typami ogólnymi i zwracają wartość, użyj <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="92ada-196">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="92ada-197">`GenericMethod` jest wywoływana bezpośrednio na obiekcie z typem.</span><span class="sxs-lookup"><span data-stu-id="92ada-197">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="92ada-198">W poniższym przykładzie przyjęto założenie, że `GenericMethod` jest dostępny z `JsInteropClasses` przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="92ada-198">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="92ada-199">Elementy odniesienia między składnikami</span><span class="sxs-lookup"><span data-stu-id="92ada-199">Reference elements across components</span></span>

<span data-ttu-id="92ada-200"><xref:Microsoft.AspNetCore.Components.ElementReference>Nie można przekazywać między składnikami, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="92ada-200">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="92ada-201">Wystąpienie jest gwarantowane, że istnieje tylko po wyrenderowaniu składnika, który jest w trakcie lub po wykonaniu <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> metody składnika.</span><span class="sxs-lookup"><span data-stu-id="92ada-201">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="92ada-202">Element <xref:Microsoft.AspNetCore.Components.ElementReference> to a [`struct`](/csharp/language-reference/builtin-types/struct) , którego nie można przesłać jako [parametru składnika](xref:blazor/components/index#component-parameters).</span><span class="sxs-lookup"><span data-stu-id="92ada-202">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="92ada-203">Aby składnik nadrzędny mógł udostępnić odwołanie do elementu innym składnikom, składnik nadrzędny może:</span><span class="sxs-lookup"><span data-stu-id="92ada-203">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="92ada-204">Zezwalaj składnikom podrzędnym na rejestrowanie wywołań zwrotnych.</span><span class="sxs-lookup"><span data-stu-id="92ada-204">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="92ada-205">Wywołaj zarejestrowane wywołania zwrotne podczas <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> zdarzenia z odwołaniem do elementu.</span><span class="sxs-lookup"><span data-stu-id="92ada-205">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="92ada-206">Pośrednio takie podejście umożliwia składnikom podrzędnym współdziałanie z odwołaniem do elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="92ada-206">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="92ada-207">Poniższy Blazor WebAssembly przykład ilustruje podejście.</span><span class="sxs-lookup"><span data-stu-id="92ada-207">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="92ada-208">W programie `<head>` z `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="92ada-208">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="92ada-209">W programie `<body>` z `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="92ada-209">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="92ada-210">`Pages/Index.razor` (składnik nadrzędny):</span><span class="sxs-lookup"><span data-stu-id="92ada-210">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="92ada-211">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="92ada-211">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="92ada-212">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="92ada-212">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="92ada-213">`Shared/SurveyPrompt.razor` (składnik podrzędny):</span><span class="sxs-lookup"><span data-stu-id="92ada-213">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="92ada-214">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="92ada-214">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="92ada-215">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="92ada-215">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="92ada-216">Zabezpieczenia wywołań międzyoperacyjnych w ramach funkcjonalności JS</span><span class="sxs-lookup"><span data-stu-id="92ada-216">Harden JS interop calls</span></span>

<span data-ttu-id="92ada-217">Usługa JS Interop może zakończyć się niepowodzeniem z powodu błędów sieci i powinna być traktowana jako niezawodna.</span><span class="sxs-lookup"><span data-stu-id="92ada-217">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="92ada-218">Domyślnie Blazor Server aplikacja przeprowadzi czas wywołań międzyoperacyjnych js na serwerze po jednej minucie.</span><span class="sxs-lookup"><span data-stu-id="92ada-218">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="92ada-219">Jeśli aplikacja może tolerować bardziej agresywny limit czasu, należy ustawić limit czasu przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="92ada-219">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="92ada-220">Globalnie w programie `Startup.ConfigureServices` Określ limit czasu:</span><span class="sxs-lookup"><span data-stu-id="92ada-220">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="92ada-221">Dla wywołania w kodzie składnika pojedyncze wywołanie może określać limit czasu:</span><span class="sxs-lookup"><span data-stu-id="92ada-221">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="92ada-222">Więcej informacji o wyczerpaniu zasobów znajduje się w temacie <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="92ada-222">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="92ada-223">Unikaj cyklicznych odwołań do obiektów</span><span class="sxs-lookup"><span data-stu-id="92ada-223">Avoid circular object references</span></span>

<span data-ttu-id="92ada-224">Obiekty, które zawierają odwołania cykliczne, nie mogą być serializowane na kliencie dla obu:</span><span class="sxs-lookup"><span data-stu-id="92ada-224">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="92ada-225">Wywołania metody .NET.</span><span class="sxs-lookup"><span data-stu-id="92ada-225">.NET method calls.</span></span>
* <span data-ttu-id="92ada-226">Wywołania metody JavaScript z języka C#, gdy typem zwracanym są odwołania cykliczne.</span><span class="sxs-lookup"><span data-stu-id="92ada-226">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="92ada-227">Aby uzyskać więcej informacji, zobacz [odwołania cykliczne nie są obsługiwane, zrób dwa (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).</span><span class="sxs-lookup"><span data-stu-id="92ada-227">For more information, see [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="blazor-javascript-isolation-and-object-references"></a><span data-ttu-id="92ada-228">Blazor Izolacja kodu JavaScript i odwołania do obiektów</span><span class="sxs-lookup"><span data-stu-id="92ada-228">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="92ada-229">Blazor Włącza izolację JavaScript w standardowych [modułach języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="92ada-229">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="92ada-230">Izolacja JavaScript zapewnia następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="92ada-230">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="92ada-231">Zaimportowana wartość JavaScript nie jest już zanieczyszczana globalną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="92ada-231">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="92ada-232">Odbiorcy biblioteki i składników nie są zobowiązani do zaimportowania powiązanego języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-232">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="92ada-233">Na przykład poniższy moduł JavaScript eksportuje funkcję JavaScript do wyświetlania monitu przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="92ada-233">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="92ada-234">Dodaj poprzedni moduł JavaScript do biblioteki .NET jako statyczny element zawartości sieci Web ( `wwwroot/exampleJsInterop.js` ), a następnie zaimportuj moduł do kodu .NET, wywołując <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> <xref:Microsoft.JSInterop.IJSRuntime> usługę.</span><span class="sxs-lookup"><span data-stu-id="92ada-234">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> on the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="92ada-235">Usługa jest wstrzykiwana jako `js` (niepokazywana) dla następującego przykładu:</span><span class="sxs-lookup"><span data-stu-id="92ada-235">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="92ada-236">`import`Identyfikator w poprzednim przykładzie jest specjalnym identyfikatorem używanym specjalnie do importowania modułu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-236">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="92ada-237">Określ moduł przy użyciu jego stabilnej statycznej ścieżki zasobów sieci Web: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="92ada-237">Specify the module using its stable static web asset path: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="92ada-238">Segment ścieżki dla bieżącego katalogu ( `./` ) jest wymagany, aby można było utworzyć poprawną statyczną ścieżkę zasobu do pliku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-238">The path segment for the current directory (`./`) is required in order to create the correct static asset path to the JavaScript file.</span></span> <span data-ttu-id="92ada-239">Dynamiczne Importowanie modułu wymaga żądania sieci, więc można je osiągnąć tylko asynchronicznie, wywołując metodę <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="92ada-239">Dynamically importing a module requires a network request, so it can only be achieved asynchronously by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="92ada-240">`{LIBRARY NAME}`Symbol zastępczy jest nazwą biblioteki.</span><span class="sxs-lookup"><span data-stu-id="92ada-240">The `{LIBRARY NAME}` placeholder is the library name.</span></span> <span data-ttu-id="92ada-241">`{PATH UNDER WWWROOT}`Symbol zastępczy jest ścieżką do skryptu w sekcji `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="92ada-241">The `{PATH UNDER WWWROOT}` placeholder is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="92ada-242"><xref:Microsoft.JSInterop.IJSRuntime> Importuje moduł jako `IJSObjectReference` , który reprezentuje odwołanie do obiektu JavaScript z kodu platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="92ada-242"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="92ada-243">Użyj `IJSObjectReference` do wywołania wyeksportowanych funkcji języka JavaScript z modułu:</span><span class="sxs-lookup"><span data-stu-id="92ada-243">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="92ada-244">`IJSInProcessObjectReference` reprezentuje odwołanie do obiektu JavaScript, którego funkcje mogą być wywoływane synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="92ada-244">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="92ada-245">Korzystanie z bibliotek języka JavaScript, które renderują interfejs użytkownika (elementy DOM)</span><span class="sxs-lookup"><span data-stu-id="92ada-245">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="92ada-246">Czasami warto używać bibliotek języka JavaScript, które tworzą widoczne elementy interfejsu użytkownika w modelu DOM przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="92ada-246">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="92ada-247">Na pierwszy rzut oka może wydawać się trudne, ponieważ Blazor system różnicowania polega na kontroli nad drzewem elementów Dom i uruchamianiu do błędów, jeśli jakiś kod zewnętrzny przydzieli drzewo Dom i unieważnia swój mechanizm do zastosowania różnic.</span><span class="sxs-lookup"><span data-stu-id="92ada-247">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="92ada-248">Nie jest to Blazor specyficzne ograniczenie.</span><span class="sxs-lookup"><span data-stu-id="92ada-248">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="92ada-249">To samo wyzwanie występuje w przypadku wszystkich opartych na różnicach struktur interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92ada-249">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="92ada-250">Na szczęście proste osadzanie wygenerowanego zewnętrznie interfejsu użytkownika w Blazor interfejsie użytkownika składnika jest niezawodne.</span><span class="sxs-lookup"><span data-stu-id="92ada-250">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="92ada-251">Zalecaną techniką jest `.razor` utworzenie pustego elementu przez kod składnika (plik).</span><span class="sxs-lookup"><span data-stu-id="92ada-251">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="92ada-252">W odniesieniu do Blazor systemu różnicowego, element jest zawsze pusty, dlatego moduł renderowania nie odnosi się do elementu i zamiast niego opuszcza jego zawartość.</span><span class="sxs-lookup"><span data-stu-id="92ada-252">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="92ada-253">Dzięki temu można bezpiecznie wypełnić element dowolną zewnętrznie zarządzaną zawartością.</span><span class="sxs-lookup"><span data-stu-id="92ada-253">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="92ada-254">Poniższy przykład demonstruje koncepcję.</span><span class="sxs-lookup"><span data-stu-id="92ada-254">The following example demonstrates the concept.</span></span> <span data-ttu-id="92ada-255">W `if` instrukcji when `firstRender` `true` , zrób coś z `myElement` .</span><span class="sxs-lookup"><span data-stu-id="92ada-255">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="92ada-256">Na przykład Wywołaj zewnętrzną bibliotekę JavaScript w celu jej wypełnienia.</span><span class="sxs-lookup"><span data-stu-id="92ada-256">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="92ada-257">Blazor pozostawia zawartość elementu tylko do momentu usunięcia tego składnika.</span><span class="sxs-lookup"><span data-stu-id="92ada-257">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="92ada-258">Po usunięciu składnika zostanie również usunięta cała poddrzewo DOM składnika.</span><span class="sxs-lookup"><span data-stu-id="92ada-258">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

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

<span data-ttu-id="92ada-259">Jako bardziej szczegółowy przykład rozważmy poniższy składnik, który renderuje interaktywną mapę przy użyciu [interfejsów API MapBox typu open source](https://www.mapbox.com/):</span><span class="sxs-lookup"><span data-stu-id="92ada-259">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="92ada-260">Odpowiedni moduł JavaScript, który powinien być umieszczony w `wwwroot/mapComponent.js` , jest następujący:</span><span class="sxs-lookup"><span data-stu-id="92ada-260">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="92ada-261">W poprzednim przykładzie Zastąp ciąg `{ACCESS TOKEN}` prawidłowym tokenem dostępu, z którego można uzyskać dostęp https://account.mapbox.com .</span><span class="sxs-lookup"><span data-stu-id="92ada-261">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="92ada-262">Aby wygenerować poprawne style, Dodaj następujący tag arkusza stylów do strony HTML hosta ( `index.html` lub `_Host.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="92ada-262">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="92ada-263">W powyższym przykładzie jest tworzony interaktywny interfejs użytkownika mapy, w którym użytkownik:</span><span class="sxs-lookup"><span data-stu-id="92ada-263">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="92ada-264">Można przeciągnąć, aby przewinąć lub powiększyć.</span><span class="sxs-lookup"><span data-stu-id="92ada-264">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="92ada-265">Kliknij przycisk przyciski, aby przejść do wstępnie zdefiniowanych lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="92ada-265">Click buttons to jump to predefined locations.</span></span>

![Mapa MapBox ulica Tokio, Japonia z przyciskami do wyboru Bristol, Zjednoczone Królestwo i Tokio, Japonia](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="92ada-267">Najważniejsze kwestie, które należy zrozumieć:</span><span class="sxs-lookup"><span data-stu-id="92ada-267">The key points to understand are:</span></span>

 * <span data-ttu-id="92ada-268">Wartość `<div>` with `@ref="mapElement"` ma wartość pustą Blazor .</span><span class="sxs-lookup"><span data-stu-id="92ada-268">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="92ada-269">Z tego względu `mapbox-gl.js` można bezpiecznie wypełnić i zmodyfikować jego zawartość w czasie.</span><span class="sxs-lookup"><span data-stu-id="92ada-269">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="92ada-270">Tej techniki można użyć z dowolną biblioteką języka JavaScript, która renderuje interfejs użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92ada-270">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="92ada-271">Można nawet osadzić składniki z struktury SPA w języku JavaScript innej firmy w Blazor składnikach programu, o ile nie spróbują się dowiedzieć i modyfikować innych części strony.</span><span class="sxs-lookup"><span data-stu-id="92ada-271">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="92ada-272">*Nie* jest bezpieczny dla zewnętrznego kodu JavaScript, aby modyfikować elementy, które nie są Blazor traktowane jako puste.</span><span class="sxs-lookup"><span data-stu-id="92ada-272">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="92ada-273">Korzystając z tego podejścia, należy wziąć pod uwagę reguły dotyczące sposobu Blazor zachowywania lub niszczenia elementów dom.</span><span class="sxs-lookup"><span data-stu-id="92ada-273">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="92ada-274">W poprzednim przykładzie składnik bezpiecznie obsługuje przycisk kliknij zdarzenia i aktualizuje istniejące wystąpienie mapy, ponieważ elementy DOM są zachowywane w miarę możliwości domyślnie.</span><span class="sxs-lookup"><span data-stu-id="92ada-274">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="92ada-275">Jeśli renderuje listę elementów mapy z wewnątrz `@foreach` pętli, chcesz użyć, `@key` Aby zapewnić zachowywanie wystąpień składników.</span><span class="sxs-lookup"><span data-stu-id="92ada-275">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="92ada-276">W przeciwnym razie zmiany w danych listy mogą spowodować wystąpienie składników, aby zachować stan poprzednich wystąpień w niepożądany sposób.</span><span class="sxs-lookup"><span data-stu-id="92ada-276">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="92ada-277">Aby uzyskać więcej informacji, zobacz [Używanie @key do zachowywania elementów i składników](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="92ada-277">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="92ada-278">Ponadto w poprzednim przykładzie pokazano, jak można hermetyzować logikę JavaScript i zależności w module ES6 i ładować ją dynamicznie przy użyciu `import` identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="92ada-278">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="92ada-279">Aby uzyskać więcej informacji, zobacz temat [izolacja JavaScript i odwołania do obiektów](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="92ada-279">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="92ada-280">Limity rozmiaru dla wywołań międzyoperacyjnych w JS</span><span class="sxs-lookup"><span data-stu-id="92ada-280">Size limits on JS interop calls</span></span>

<span data-ttu-id="92ada-281">W programie Blazor WebAssembly platforma nie nakłada limitu rozmiaru danych wejściowych i wyjściowych w programie js.</span><span class="sxs-lookup"><span data-stu-id="92ada-281">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="92ada-282">W programie Blazor Server wywołania programu js Interop są ograniczone przez maksymalny SignalR rozmiar przychodzących komunikatów dozwolony dla metod koncentratora, które są wymuszane przez <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (domyślnie: 32 KB).</span><span class="sxs-lookup"><span data-stu-id="92ada-282">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="92ada-283">Program JS do SignalR komunikatów programu .NET większych niż <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> Zgłoś błąd.</span><span class="sxs-lookup"><span data-stu-id="92ada-283">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="92ada-284">Platforma nie nakłada limitu rozmiaru SignalR komunikatu z centrum na klienta.</span><span class="sxs-lookup"><span data-stu-id="92ada-284">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span> <span data-ttu-id="92ada-285">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span><span class="sxs-lookup"><span data-stu-id="92ada-285">For more information, see <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span></span>
  
## <a name="js-modules"></a><span data-ttu-id="92ada-286">Moduły JS</span><span class="sxs-lookup"><span data-stu-id="92ada-286">JS modules</span></span>

<span data-ttu-id="92ada-287">W przypadku izolacji JS program JS współdziała z domyślną obsługą języka [ECMAScript (](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) [model ECMAScript](https://tc39.es/ecma262/#sec-modules)) w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="92ada-287">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="unmarshalled-js-interop"></a><span data-ttu-id="92ada-288">Nieskierowany międzyoperacyjna JS</span><span class="sxs-lookup"><span data-stu-id="92ada-288">Unmarshalled JS interop</span></span>

<span data-ttu-id="92ada-289">Blazor WebAssembly składniki mogą mieć niską wydajność, gdy obiekty .NET są serializowane pod kątem współdziałania z JS i jeden z następujących warunków jest spełniony:</span><span class="sxs-lookup"><span data-stu-id="92ada-289">Blazor WebAssembly components may experience poor performance when .NET objects are serialized for JS interop and either of the following are true:</span></span>

* <span data-ttu-id="92ada-290">Duże ilości obiektów .NET są szybko serializowane.</span><span class="sxs-lookup"><span data-stu-id="92ada-290">A high volume of .NET objects are rapidly serialized.</span></span> <span data-ttu-id="92ada-291">Przykład: wywołania międzyoperacyjnego JS są wykonywane na podstawie przeniesienia urządzenia wejściowego, takiego jak wirowanie kółka myszy.</span><span class="sxs-lookup"><span data-stu-id="92ada-291">Example: JS interop calls are made on the basis of moving an input device, such as spinning a mouse wheel.</span></span>
* <span data-ttu-id="92ada-292">Duże obiekty .NET lub wiele obiektów .NET muszą być serializowane dla międzyoperacyjnego JS.</span><span class="sxs-lookup"><span data-stu-id="92ada-292">Large .NET objects or many .NET objects must be serialized for JS interop.</span></span> <span data-ttu-id="92ada-293">Przykład: wywołania międzyoperacyjne JS wymagają serializowania dziesiątek plików.</span><span class="sxs-lookup"><span data-stu-id="92ada-293">Example: JS interop calls require serializing dozens of files.</span></span>

<span data-ttu-id="92ada-294"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> reprezentuje odwołanie do obiektu JavaScript, którego funkcje mogą być wywoływane bez nakładu na Serializowanie danych platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="92ada-294"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span>

<span data-ttu-id="92ada-295">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="92ada-295">In the following example:</span></span>

* <span data-ttu-id="92ada-296">[Struktura](/dotnet/csharp/language-reference/builtin-types/struct) zawierająca ciąg i liczbę całkowitą są przesyłane do kodu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-296">A [struct](/dotnet/csharp/language-reference/builtin-types/struct) containing a string and an integer is passed unserialized to JavaScript.</span></span>
* <span data-ttu-id="92ada-297">Funkcje języka JavaScript przetwarzają dane i zwracają wartość logiczną lub ciąg do obiektu wywołującego.</span><span class="sxs-lookup"><span data-stu-id="92ada-297">JavaScript functions process the data and return either a boolean or string to the caller.</span></span>
* <span data-ttu-id="92ada-298">Ciąg JavaScript nie jest bezpośrednio konwertowany do obiektu platformy .NET `string` .</span><span class="sxs-lookup"><span data-stu-id="92ada-298">A JavaScript string isn't directly convertible into a .NET `string` object.</span></span> <span data-ttu-id="92ada-299">`unmarshalledFunctionReturnString`Funkcja wywołuje funkcję `BINDING.js_string_to_mono_string` do zarządzania konwersją ciągu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-299">The `unmarshalledFunctionReturnString` function calls `BINDING.js_string_to_mono_string` to manage the conversion of a Javascript string.</span></span>

> [!NOTE]
> <span data-ttu-id="92ada-300">Poniższe przykłady nie są typowymi przypadkami użycia w tym scenariuszu, ponieważ [Struktura](/dotnet/csharp/language-reference/builtin-types/struct) przeniesiona do języka JavaScript nie powoduje niskiej wydajności składników.</span><span class="sxs-lookup"><span data-stu-id="92ada-300">The following examples aren't typical use cases for this scenario because the [struct](/dotnet/csharp/language-reference/builtin-types/struct) passed to JavaScript doesn't result in poor component performance.</span></span> <span data-ttu-id="92ada-301">W przykładzie jest wykorzystywany mały obiekt tylko w celu przedstawienia koncepcji przekazywania nieserializowanych danych platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="92ada-301">The example uses a small object merely to demonstrate the concepts for passing unserialized .NET data.</span></span>

<span data-ttu-id="92ada-302">Zawartość `<script>` bloku w `wwwroot/index.html` lub zewnętrznym pliku JavaScript, do którego odwołuje się `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="92ada-302">Content of a `<script>` block in `wwwroot/index.html` or an external Javascript file referenced by `wwwroot/index.html`:</span></span>

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> <span data-ttu-id="92ada-303">`js_string_to_mono_string`Nazwa funkcji, zachowanie i istnienie mogą ulec zmianie w przyszłej wersji platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="92ada-303">The `js_string_to_mono_string` function name, behavior, and existence is subject to change in a future release of .NET.</span></span> <span data-ttu-id="92ada-304">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="92ada-304">For example:</span></span>
>
> * <span data-ttu-id="92ada-305">Prawdopodobnie zmieniono nazwę funkcji.</span><span class="sxs-lookup"><span data-stu-id="92ada-305">The function is likely to be renamed.</span></span>
> * <span data-ttu-id="92ada-306">Sama funkcja może zostać usunięta na korzyść automatycznej konwersji ciągów przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="92ada-306">The function itself might be removed in favor of automatic conversion of strings by the framework.</span></span>

<span data-ttu-id="92ada-307">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop` ):</span><span class="sxs-lookup"><span data-stu-id="92ada-307">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span></span>

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

<span data-ttu-id="92ada-308">Jeśli `IJSUnmarshalledObjectReference` wystąpienie nie zostanie usunięte w kodzie C#, można je usunąć w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92ada-308">If an `IJSUnmarshalledObjectReference` instance isn't disposed in C# code, it can be disposed in JavaScript.</span></span> <span data-ttu-id="92ada-309">Następująca `dispose` Funkcja usuwa odwołanie do obiektu w przypadku wywołania z JavaScript:</span><span class="sxs-lookup"><span data-stu-id="92ada-309">The following `dispose` function disposes the object reference when called from JavaScript:</span></span>

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

<span data-ttu-id="92ada-310">Typy tablic mogą być konwertowane z obiektów JavaScript do obiektów .NET przy użyciu `js_typed_array_to_array` , ale tablica JavaScript musi być tablicą z określonym typem.</span><span class="sxs-lookup"><span data-stu-id="92ada-310">Array types can be converted from JavaScript objects into .NET objects using `js_typed_array_to_array`, but the JavaScript array must be a typed array.</span></span> <span data-ttu-id="92ada-311">Tablice z JavaScript mogą być odczytywane w kodzie C# jako tablica obiektów .NET ( `object[]` ).</span><span class="sxs-lookup"><span data-stu-id="92ada-311">Arrays from JavaScript can be read in C# code as a .NET object array (`object[]`).</span></span>

<span data-ttu-id="92ada-312">Inne typy danych, takie jak tablice ciągów, można przekonwertować, ale wymagają utworzenia nowego obiektu tablicy mono ( `mono_obj_array_new` ) i ustawienia jego wartości ( `mono_obj_array_set` ).</span><span class="sxs-lookup"><span data-stu-id="92ada-312">Other data types, such as string arrays, can be converted but require creating a new Mono array object (`mono_obj_array_new`) and setting its value (`mono_obj_array_set`).</span></span>

> [!WARNING]
> <span data-ttu-id="92ada-313">Funkcje języka JavaScript udostępniane przez Blazor platformę, takie jak `js_typed_array_to_array` , `mono_obj_array_new` i `mono_obj_array_set` , podlegają zmianom nazw, zmianom behawioralnym lub usunięciu w przyszłych wersjach platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="92ada-313">JavaScript functions provided by the Blazor framework, such as `js_typed_array_to_array`, `mono_obj_array_new`, and `mono_obj_array_set`, are subject to name changes, behavioral changes, or removal in future releases of .NET.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92ada-314">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="92ada-314">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="92ada-315">InteropComponent. Razor — przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałąź wydania)</span><span class="sxs-lookup"><span data-stu-id="92ada-315">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
