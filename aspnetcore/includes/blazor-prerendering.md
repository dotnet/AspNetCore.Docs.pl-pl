---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659720"
---
<span data-ttu-id="2e273-101">Podczas Blazor gdy aplikacja Server jest prerendering, niektóre akcje, takie jak wywołanie javascript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało ustanowione.</span><span class="sxs-lookup"><span data-stu-id="2e273-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="2e273-102">Składniki mogą wymagać renderowania inaczej podczas prerendered.</span><span class="sxs-lookup"><span data-stu-id="2e273-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="2e273-103">Aby opóźnić połączenia interop JavaScript do momentu nawiązania połączenia z przeglądarką, można użyć [zdarzenia cyklu życia składnika OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="2e273-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="2e273-104">To zdarzenie jest wywoływane tylko po aplikacji jest w pełni renderowane i nawiązywanego połączenia klienta.</span><span class="sxs-lookup"><span data-stu-id="2e273-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="2e273-105">W przypadku poprzedniego przykładowego `setElementText` kodu podaj `<head>` funkcję JavaScript wewnątrzBlazor elementu *wwwroot/index.html* (WebAssembly) lub *Pages/_Host.cshtml* (Blazor Serwer).</span><span class="sxs-lookup"><span data-stu-id="2e273-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="2e273-106">Funkcja jest wywoływana z `IJSRuntime.InvokeVoidAsync` i nie zwraca wartości:</span><span class="sxs-lookup"><span data-stu-id="2e273-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="2e273-107">W poprzednim przykładzie modyfikuje model obiektu dokumentu (DOM) bezpośrednio tylko w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="2e273-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="2e273-108">Bezpośrednie modyfikowanie dom z JavaScript nie jest zalecane w większości scenariuszy, ponieważ JavaScript może zakłócać Blazorśledzenia zmian.</span><span class="sxs-lookup"><span data-stu-id="2e273-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="2e273-109">Poniższy składnik pokazuje, jak używać javascript interop jako część logiki inicjowania składnika w sposób, który jest zgodny z prerendering.</span><span class="sxs-lookup"><span data-stu-id="2e273-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="2e273-110">Składnik pokazuje, że możliwe jest wyzwolenie aktualizacji `OnAfterRenderAsync`renderowania od wewnątrz .</span><span class="sxs-lookup"><span data-stu-id="2e273-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="2e273-111">Deweloper musi unikać tworzenia nieskończonej pętli w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="2e273-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="2e273-112">Gdzie `JSRuntime.InvokeAsync` jest `ElementRef` wywoływana, `OnAfterRenderAsync` jest używany tylko w i nie w dowolnej wcześniejszej metody cyklu życia, ponieważ nie ma żadnego elementu JavaScript, dopóki po renderowane składnika.</span><span class="sxs-lookup"><span data-stu-id="2e273-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="2e273-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) jest wywoływana do rerender składnika z nowym stanie uzyskanych z wywołania interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2e273-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="2e273-114">Kod nie tworzy nieskończonej pętli, ponieważ `StateHasChanged` `infoFromJs` jest `null`wywoływana tylko wtedy, gdy jest .</span><span class="sxs-lookup"><span data-stu-id="2e273-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="2e273-115">W przypadku poprzedniego przykładowego `setElementText` kodu podaj `<head>` funkcję JavaScript wewnątrzBlazor elementu *wwwroot/index.html* (WebAssembly) lub *Pages/_Host.cshtml* (Blazor Serwer).</span><span class="sxs-lookup"><span data-stu-id="2e273-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="2e273-116">Funkcja jest wywoływana z `IJSRuntime.InvokeAsync` i zwraca wartość:</span><span class="sxs-lookup"><span data-stu-id="2e273-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="2e273-117">W poprzednim przykładzie modyfikuje model obiektu dokumentu (DOM) bezpośrednio tylko w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="2e273-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="2e273-118">Bezpośrednie modyfikowanie dom z JavaScript nie jest zalecane w większości scenariuszy, ponieważ JavaScript może zakłócać Blazorśledzenia zmian.</span><span class="sxs-lookup"><span data-stu-id="2e273-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
