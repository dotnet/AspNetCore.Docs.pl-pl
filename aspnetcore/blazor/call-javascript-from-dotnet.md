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
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="2ce50-103">Wywoływanie funkcji JavaScript z metod .NET w ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="2ce50-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="2ce50-104">[Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27)i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2ce50-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2ce50-105">Aplikacja Blazor może wywoływać funkcje JavaScript z metod .NET i .NET z funkcji JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ce50-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="2ce50-106">Scenariusze te są nazywane *interoperacyjnością JavaScript* *(JS interop*).</span><span class="sxs-lookup"><span data-stu-id="2ce50-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="2ce50-107">W tym artykule omówiono wywoływanie funkcji JavaScript z platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="2ce50-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="2ce50-108">Aby uzyskać informacje na temat wywoływania metod <xref:blazor/call-dotnet-from-javascript>platformy .NET z języka JavaScript, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2ce50-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="2ce50-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2ce50-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2ce50-110">Aby wywołać javascript z platformy `IJSRuntime` .NET, użyj abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="2ce50-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="2ce50-111">Aby wystawić wywołania interop JS, wstrzyknąć `IJSRuntime` abstrakcję w składniku.</span><span class="sxs-lookup"><span data-stu-id="2ce50-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="2ce50-112">Metoda `InvokeAsync<T>` przyjmuje identyfikator funkcji JavaScript, który chcesz wywołać wraz z dowolną liczbą argumentów serializable JSON.</span><span class="sxs-lookup"><span data-stu-id="2ce50-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="2ce50-113">Identyfikator funkcji jest względem zakresu globalnego`window`( ).</span><span class="sxs-lookup"><span data-stu-id="2ce50-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="2ce50-114">Jeśli chcesz zadzwonić, `window.someScope.someFunction`identyfikator `someScope.someFunction`jest .</span><span class="sxs-lookup"><span data-stu-id="2ce50-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="2ce50-115">Nie ma potrzeby rejestrowania funkcji, zanim ją wywoła.</span><span class="sxs-lookup"><span data-stu-id="2ce50-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="2ce50-116">Zwracany `T` typ musi być również serializable JSON.</span><span class="sxs-lookup"><span data-stu-id="2ce50-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="2ce50-117">`T`powinien być zgodny z typem .NET, który najlepiej mapuje zwracany typ JSON.</span><span class="sxs-lookup"><span data-stu-id="2ce50-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="2ce50-118">W Blazor przypadku aplikacji serwera z włączoną funkcją wstępnego wywoływania w języku JavaScript nie jest możliwe podczas wstępnego wstępnego wstępnego wstępnego wstępnego rozsyłania.</span><span class="sxs-lookup"><span data-stu-id="2ce50-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="2ce50-119">Połączenia interop JavaScript muszą zostać odroczone do czasu nawiązania połączenia z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="2ce50-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="2ce50-120">Aby uzyskać więcej informacji, zobacz [wykrywanie, Blazor gdy aplikacja serwer jest prerendering](#detect-when-a-blazor-server-app-is-prerendering) sekcji.</span><span class="sxs-lookup"><span data-stu-id="2ce50-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="2ce50-121">Poniższy przykład jest oparty na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), dekoder oparty na JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ce50-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="2ce50-122">W przykładzie pokazano, jak wywołać funkcję JavaScript z metody C#.</span><span class="sxs-lookup"><span data-stu-id="2ce50-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="2ce50-123">Funkcja JavaScript akceptuje tablicę bajtów z metody C#, dekoduje tablicę i zwraca tekst do składnika do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="2ce50-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="2ce50-124">Wewnątrz `<head>` elementu *wwwroot/index.html* (WebAssembly)Blazor lub *Pages/_Host.cshtml* (Serwer)Blazor podaj funkcję JavaScript, która służy `TextDecoder` do dekodowania przekazanej tablicy i zwraca wartość zdekodowaną:</span><span class="sxs-lookup"><span data-stu-id="2ce50-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="2ce50-125">Kod JavaScript, taki jak kod pokazany w poprzednim przykładzie, można również załadować z pliku JavaScript (*.js*) z odwołaniem do pliku skryptu:</span><span class="sxs-lookup"><span data-stu-id="2ce50-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="2ce50-126">Następujący składnik:</span><span class="sxs-lookup"><span data-stu-id="2ce50-126">The following component:</span></span>

* <span data-ttu-id="2ce50-127">Wywołuje funkcję `convertArray` JavaScript `JSRuntime` przy użyciu po wybraniu przycisku składnika **(Konwertuj tablicę).**</span><span class="sxs-lookup"><span data-stu-id="2ce50-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="2ce50-128">Po wywołaniu funkcji JavaScript przekazana tablica jest konwertowana na ciąg.</span><span class="sxs-lookup"><span data-stu-id="2ce50-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="2ce50-129">Ciąg jest zwracany do składnika do wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="2ce50-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="2ce50-130">Czas IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="2ce50-130">IJSRuntime</span></span>

<span data-ttu-id="2ce50-131">Aby użyć `IJSRuntime` abstrakcji, należy przyjąć dowolną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="2ce50-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="2ce50-132">Wstrzyknąć `IJSRuntime` abstrakcję do komponentu Razor (*.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="2ce50-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="2ce50-133">Wewnątrz `<head>` elementu *wwwroot/index.html* (WebAssembly)Blazor lub *Pages/_Host.cshtml* (Serwer)Blazor podaj `handleTickerChanged` funkcję JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ce50-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="2ce50-134">Funkcja jest wywoływana z `IJSRuntime.InvokeVoidAsync` i nie zwraca wartości:</span><span class="sxs-lookup"><span data-stu-id="2ce50-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="2ce50-135">Wstrzyknąć `IJSRuntime` abstrakcję do klasy (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ce50-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="2ce50-136">Wewnątrz `<head>` elementu *wwwroot/index.html* (WebAssembly)Blazor lub *Pages/_Host.cshtml* (Serwer)Blazor podaj `handleTickerChanged` funkcję JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ce50-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="2ce50-137">Funkcja jest wywoływana z `JSRuntime.InvokeAsync` i zwraca wartość:</span><span class="sxs-lookup"><span data-stu-id="2ce50-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="2ce50-138">W przypadku generowania zawartości dynamicznej `[Inject]` za pomocą [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)użyj atrybutu:</span><span class="sxs-lookup"><span data-stu-id="2ce50-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="2ce50-139">W przykładowej aplikacji po stronie klienta, która towarzyszy w tym temacie, dwie funkcje JavaScript są dostępne dla aplikacji, które współdziałają z dom do odbierania danych wejściowych użytkownika i wyświetlania wiadomości powitalnej:</span><span class="sxs-lookup"><span data-stu-id="2ce50-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="2ce50-140">`showPrompt`&ndash; Generuje monit o zaakceptowanie danych wejściowych użytkownika (nazwa użytkownika) i zwraca nazwę do osoby dzwoniącej.</span><span class="sxs-lookup"><span data-stu-id="2ce50-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="2ce50-141">`displayWelcome`&ndash; Przypisuje wiadomość powitalną od osoby dzwoniącej do `id` `welcome`obiektu DOM z elementem .</span><span class="sxs-lookup"><span data-stu-id="2ce50-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="2ce50-142">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="2ce50-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="2ce50-143">Umieść `<script>` znacznik, który odwołuje się do *wwwroot/index.html* pliku JavaScriptBlazor w pliku wwwroot/index.html (WebAssembly) lub *Pages/_Host.cshtml* Blazor (Serwer).</span><span class="sxs-lookup"><span data-stu-id="2ce50-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="2ce50-144">*wwwroot/index.html* (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="2ce50-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="2ce50-145">*Strony/_Host.cshtml* (Blazor Serwer):</span><span class="sxs-lookup"><span data-stu-id="2ce50-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="2ce50-146">Nie umieszczaj `<script>` znacznika w pliku `<script>` składnika, ponieważ tag nie może być aktualizowany dynamicznie.</span><span class="sxs-lookup"><span data-stu-id="2ce50-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="2ce50-147">.NET metody współdziałania z funkcjami JavaScript w *pliku exampleJsInterop.js* przez wywołanie `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="2ce50-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="2ce50-148">Abstrakcja `IJSRuntime` jest asynchroniza, Blazor aby umożliwić scenariusze serwera.</span><span class="sxs-lookup"><span data-stu-id="2ce50-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="2ce50-149">Jeśli aplikacja jest Blazor aplikacją WebAssembly i chcesz wywołać funkcję JavaScript synchronicznie, zamiast tego można go `IJSInProcessRuntime` przesunąć w dół i wywołać. `Invoke<T>`</span><span class="sxs-lookup"><span data-stu-id="2ce50-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="2ce50-150">Zaleca się, że większość bibliotek interop JS używać asynchronizycznych interfejsów API, aby upewnić się, że biblioteki są dostępne we wszystkich scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="2ce50-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="2ce50-151">Przykładowa aplikacja zawiera składnik, aby zademonstrować js interop.</span><span class="sxs-lookup"><span data-stu-id="2ce50-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="2ce50-152">Składnik:</span><span class="sxs-lookup"><span data-stu-id="2ce50-152">The component:</span></span>

* <span data-ttu-id="2ce50-153">Odbiera dane wejściowe użytkownika za pomocą monitu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ce50-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="2ce50-154">Zwraca tekst do składnika do przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="2ce50-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="2ce50-155">Wywołuje drugą funkcję JavaScript, która współdziała z dom, aby wyświetlić wiadomość powitalną.</span><span class="sxs-lookup"><span data-stu-id="2ce50-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="2ce50-156">*Strony/JSInterop.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="2ce50-156">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="2ce50-157">Po `TriggerJsPrompt` wykonaniu przez wybranie składnika **Trigger JavaScript Prompt** `showPrompt` przycisk, funkcja JavaScript pod warunkiem, że w *pliku wwwroot/exampleJsInterop.js* jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="2ce50-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="2ce50-158">Funkcja `showPrompt` akceptuje dane wejściowe użytkownika (nazwa użytkownika), który jest zakodowany w formacie HTML i zwracany do składnika.</span><span class="sxs-lookup"><span data-stu-id="2ce50-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="2ce50-159">Składnik przechowuje nazwę użytkownika w zmiennej `name`lokalnej, .</span><span class="sxs-lookup"><span data-stu-id="2ce50-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="2ce50-160">Ciąg przechowywany `name` w jest włączony do wiadomości powitalnej, która jest `displayWelcome`przekazywana do funkcji JavaScript, która renderuje wiadomość powitalną do tagu nagłówka.</span><span class="sxs-lookup"><span data-stu-id="2ce50-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="2ce50-161">Wywoływanie funkcji JavaScript unieważnienia</span><span class="sxs-lookup"><span data-stu-id="2ce50-161">Call a void JavaScript function</span></span>

<span data-ttu-id="2ce50-162">Funkcje JavaScript, które zwracają [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) `IJSRuntime.InvokeVoidAsync`lub [undefined,](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) są wywoływane z .</span><span class="sxs-lookup"><span data-stu-id="2ce50-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a><span data-ttu-id="2ce50-163">Wykrywanie, Blazor kiedy aplikacja serwera jest prerendering</span><span class="sxs-lookup"><span data-stu-id="2ce50-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="2ce50-164">Przechwytywanie odniesień do elementów</span><span class="sxs-lookup"><span data-stu-id="2ce50-164">Capture references to elements</span></span>

<span data-ttu-id="2ce50-165">Niektóre scenariusze interop JS wymagają odwołań do elementów HTML.</span><span class="sxs-lookup"><span data-stu-id="2ce50-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="2ce50-166">Na przykład biblioteka interfejsu użytkownika może wymagać odwołania do elementu do inicjowania lub może być `focus` `play`konieczne wywołanie interfejsów API podobnych do polecenia na elemencie, takim jak lub .</span><span class="sxs-lookup"><span data-stu-id="2ce50-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="2ce50-167">Przechwytywanie odwołań do elementów HTML w składniku przy użyciu następującego podejścia:</span><span class="sxs-lookup"><span data-stu-id="2ce50-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="2ce50-168">Dodaj `@ref` atrybut do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="2ce50-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="2ce50-169">Zdefiniuj `ElementReference` pole typu, którego `@ref` nazwa odpowiada wartości atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2ce50-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="2ce50-170">Poniższy przykład pokazuje przechwytywanie `username` `<input>` odwołania do elementu:</span><span class="sxs-lookup"><span data-stu-id="2ce50-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="2ce50-171">Użyj tylko odwołania do elementu, aby zmutować zawartość pustego elementu, który nie wchodzi w interakcję z Blazorprogramem .</span><span class="sxs-lookup"><span data-stu-id="2ce50-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="2ce50-172">Ten scenariusz jest przydatny, gdy interfejs API innej firmy dostarcza zawartość do elementu.</span><span class="sxs-lookup"><span data-stu-id="2ce50-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="2ce50-173">Ponieważ Blazor nie wchodzi w interakcję z elementem, nie ma Blazormożliwości konfliktu między reprezentacji elementu i DOM.</span><span class="sxs-lookup"><span data-stu-id="2ce50-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="2ce50-174">W poniższym przykładzie *niebezpieczne* jest mutowanie zawartości listy nieuporządkowanej (`ul`), ponieważ Blazor współdziała z dom,`<li>`aby wypełnić elementy listy tego elementu ( ):</span><span class="sxs-lookup"><span data-stu-id="2ce50-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="2ce50-175">Jeśli JS interop mutuje `MyList` zawartość Blazor elementu i próbuje zastosować różnice do elementu, różnice nie będą zgodne z DOM.</span><span class="sxs-lookup"><span data-stu-id="2ce50-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="2ce50-176">Jeśli chodzi o kod .NET, `ElementReference` jest nieprzezroczystym uchwytem.</span><span class="sxs-lookup"><span data-stu-id="2ce50-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="2ce50-177">*Jedyną* rzeczą, którą `ElementReference` możesz zrobić, to przekazać go do kodu JavaScript za pośrednictwem JS interop.</span><span class="sxs-lookup"><span data-stu-id="2ce50-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="2ce50-178">Po wykonaniu tej tej pracy kod po `HTMLElement` stronie języka JavaScript odbiera wystąpienie, którego może używać z normalnymi interfejsami API DOM.</span><span class="sxs-lookup"><span data-stu-id="2ce50-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="2ce50-179">Na przykład następujący kod definiuje metodę rozszerzenia .NET, która umożliwia ustawienie fokusu na elemencie:</span><span class="sxs-lookup"><span data-stu-id="2ce50-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="2ce50-180">*przykładJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="2ce50-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="2ce50-181">Aby wywołać funkcję JavaScript, która nie `IJSRuntime.InvokeVoidAsync`zwraca wartości, użyj .</span><span class="sxs-lookup"><span data-stu-id="2ce50-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="2ce50-182">Poniższy kod ustawia fokus na wejściu nazwy użytkownika, `ElementReference`wywołując poprzednią funkcję JavaScript z przechwyconym:</span><span class="sxs-lookup"><span data-stu-id="2ce50-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="2ce50-183">Aby użyć metody rozszerzenia, należy utworzyć metodę `IJSRuntime` rozszerzenia statycznego, która odbiera wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="2ce50-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="2ce50-184">Metoda `Focus` jest wywoływana bezpośrednio na obiekcie.</span><span class="sxs-lookup"><span data-stu-id="2ce50-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="2ce50-185">W poniższym `Focus` przykładzie przyjęto założenie, że metoda jest dostępna z `JsInteropClasses` obszaru nazw:</span><span class="sxs-lookup"><span data-stu-id="2ce50-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="2ce50-186">Zmienna `username` jest wypełniana tylko po renderowaniu składnika.</span><span class="sxs-lookup"><span data-stu-id="2ce50-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="2ce50-187">Jeśli niezaludniony `ElementReference` kod javascript zostanie przekazany do kodu `null`JavaScript, kod JavaScript otrzymuje wartość .</span><span class="sxs-lookup"><span data-stu-id="2ce50-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="2ce50-188">Aby manipulować odwołaniami do elementów po zakończeniu renderowania przez komponent (aby ustawić początkowy fokus na elemencie), należy użyć [metod cyklu życia komponentu OnAfterRenderAsync lub OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="2ce50-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="2ce50-189">Podczas pracy z typami ogólnymi i zwracania wartości należy użyć [valuetask\<T>: ](xref:System.Threading.Tasks.ValueTask`1)</span><span class="sxs-lookup"><span data-stu-id="2ce50-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="2ce50-190">`GenericMethod`jest wywoływana bezpośrednio na obiekcie z typem.</span><span class="sxs-lookup"><span data-stu-id="2ce50-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="2ce50-191">W poniższym `GenericMethod` przykładzie przyjęto `JsInteropClasses` założenie, że jest dostępna z obszaru nazw:</span><span class="sxs-lookup"><span data-stu-id="2ce50-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="2ce50-192">Elementy referencyjne między komponentami</span><span class="sxs-lookup"><span data-stu-id="2ce50-192">Reference elements across components</span></span>

<span data-ttu-id="2ce50-193">Jest `ElementReference` gwarantowana tylko prawidłowe `OnAfterRender` w metodzie składnika `struct`(i odwołanie do elementu jest ), więc odwołanie do elementu nie mogą być przekazywane między składnikami.</span><span class="sxs-lookup"><span data-stu-id="2ce50-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="2ce50-194">Aby składnik nadrzędny udostępnił odniesienie do elementu innym komponentom, składnik nadrzędny może:</span><span class="sxs-lookup"><span data-stu-id="2ce50-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="2ce50-195">Zezwalaj składnikom podrzędnym na rejestrowanie wywołań zwrotnych.</span><span class="sxs-lookup"><span data-stu-id="2ce50-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="2ce50-196">Wywołać zarejestrowanych wywołań `OnAfterRender` zwrotnych podczas zdarzenia z odwołaniem do elementu przekazywane.</span><span class="sxs-lookup"><span data-stu-id="2ce50-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="2ce50-197">Pośrednio takie podejście umożliwia składników podrzędnych do interakcji z odwołaniem elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="2ce50-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="2ce50-198">Poniższy Blazor przykład WebAssembly ilustruje podejście.</span><span class="sxs-lookup"><span data-stu-id="2ce50-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="2ce50-199">W `<head>` *wwwroot/index.html:*</span><span class="sxs-lookup"><span data-stu-id="2ce50-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="2ce50-200">W `<body>` *wwwroot/index.html:*</span><span class="sxs-lookup"><span data-stu-id="2ce50-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="2ce50-201">*Pages/Index.brzytwa* (składnik nadrzędny):</span><span class="sxs-lookup"><span data-stu-id="2ce50-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="2ce50-202">*Strony/Index.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ce50-202">*Pages/Index.razor.cs*:</span></span>

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

<span data-ttu-id="2ce50-203">*Shared/SurveyPrompt.brzytwa* (składnik podrzędny):</span><span class="sxs-lookup"><span data-stu-id="2ce50-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="2ce50-204">*Wspólne/SurveyPrompt.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ce50-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="2ce50-205">Harden JS połączeń interop</span><span class="sxs-lookup"><span data-stu-id="2ce50-205">Harden JS interop calls</span></span>

<span data-ttu-id="2ce50-206">JS interop może zakończyć się niepowodzeniem z powodu błędów sieciowych i powinny być traktowane jako zawodne.</span><span class="sxs-lookup"><span data-stu-id="2ce50-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="2ce50-207">Domyślnie Blazor aplikacja serwera upotrzyma js interop wywołania na serwerze po jednej minucie.</span><span class="sxs-lookup"><span data-stu-id="2ce50-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="2ce50-208">Jeśli aplikacja może tolerować bardziej agresywny limit czasu, na przykład 10 sekund, ustaw limit czasu przy użyciu jednego z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="2ce50-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="2ce50-209">Globalnie `Startup.ConfigureServices`w , określ limit czasu:</span><span class="sxs-lookup"><span data-stu-id="2ce50-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="2ce50-210">Na wywołanie w kodzie składnika pojedyncze wywołanie może określić limit czasu:</span><span class="sxs-lookup"><span data-stu-id="2ce50-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="2ce50-211">Aby uzyskać więcej informacji na <xref:security/blazor/server>temat wyczerpania zasobów, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2ce50-211">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="2ce50-212">Unikaj odwołań do obiektów cyklicznych</span><span class="sxs-lookup"><span data-stu-id="2ce50-212">Avoid circular object references</span></span>

<span data-ttu-id="2ce50-213">Obiekty, które zawierają odwołania cykliczne nie mogą być serializowane na kliencie dla jednego z:</span><span class="sxs-lookup"><span data-stu-id="2ce50-213">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="2ce50-214">Wywołania metody .NET.</span><span class="sxs-lookup"><span data-stu-id="2ce50-214">.NET method calls.</span></span>
* <span data-ttu-id="2ce50-215">Metoda JavaScript wywołuje z języka C#, gdy typ zwracany ma odwołania cykliczne.</span><span class="sxs-lookup"><span data-stu-id="2ce50-215">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="2ce50-216">Aby uzyskać więcej informacji, zobacz następujące problemy:</span><span class="sxs-lookup"><span data-stu-id="2ce50-216">For more information, see the following issues:</span></span>

* [<span data-ttu-id="2ce50-217">Odwołania cykliczne nie są obsługiwane, weź dwa (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="2ce50-217">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="2ce50-218">Propozycja: Dodaj mechanizm do obsługi odwołań cyklicznych podczas serializacji (#30820 dotnet/runtime)</span><span class="sxs-lookup"><span data-stu-id="2ce50-218">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="2ce50-219">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2ce50-219">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="2ce50-220">Przykład InteropComponent.razor (repozytorium Dotnet/AspNetCore GitHub, gałąź wydania 3.1)</span><span class="sxs-lookup"><span data-stu-id="2ce50-220">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="2ce50-221">[Wykonywanie dużych transferów danych w Blazor aplikacjach serwera](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="2ce50-221">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
