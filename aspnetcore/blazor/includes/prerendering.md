<span data-ttu-id="96355-101">Gdy aplikacja serwera Blazor jest wstępnie renderowana, niektóre akcje, takie jak wywoływanie kodu JavaScript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało nawiązane.</span><span class="sxs-lookup"><span data-stu-id="96355-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="96355-102">Składniki mogą być konieczne w różny sposób, gdy są wstępnie renderowane.</span><span class="sxs-lookup"><span data-stu-id="96355-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="96355-103">Aby opóźnić wywołania międzyoperacyjne języka JavaScript do momentu ustanowienia połączenia z przeglądarką, można użyć [zdarzenia cyklu życia składnika OnAfterRenderAsync](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="96355-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="96355-104">To zdarzenie jest wywoływane tylko wtedy, gdy aplikacja jest w pełni renderowana, a połączenie z klientem zostanie nawiązane.</span><span class="sxs-lookup"><span data-stu-id="96355-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

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

<span data-ttu-id="96355-105">W powyższym przykładowym kodzie Podaj `setElementText` funkcję JavaScript wewnątrz `<head>` elementu `wwwroot/index.html` (Blazor webassembly) lub `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="96355-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="96355-106">Funkcja jest wywoływana z <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> i nie zwraca wartości:</span><span class="sxs-lookup"><span data-stu-id="96355-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="96355-107">Poprzedni przykład modyfikuje Document Object Model (DOM) bezpośrednio wyłącznie w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="96355-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="96355-108">Nie zaleca się bezpośredniej modyfikacji modelu DOM przy użyciu języka JavaScript w większości scenariuszy, ponieważ kod JavaScript może zakłócać śledzenie zmian Blazor.</span><span class="sxs-lookup"><span data-stu-id="96355-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="96355-109">Poniższy składnik pokazuje, jak używać międzyoperacyjności JavaScript jako części logiki inicjalizacji składnika w sposób, który jest zgodny z renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="96355-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="96355-110">Składnik pokazuje, że można wyzwolić aktualizację renderowania z wewnątrz <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="96355-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="96355-111">Deweloper musi unikać tworzenia pętli nieskończonej w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="96355-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="96355-112">Gdzie <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> jest wywoływana, `ElementRef` jest używana tylko w, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> a nie w żadnej wcześniejszej metodzie cyklu życia, ponieważ nie istnieje element JavaScript do momentu renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="96355-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="96355-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) jest wywoływana, aby przetworzyć składnik z nowym stanem uzyskanym z wywołania międzyoperacyjności JavaScript (Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering> ).</span><span class="sxs-lookup"><span data-stu-id="96355-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call (for more information, see <xref:blazor/components/rendering>).</span></span> <span data-ttu-id="96355-114">Kod nie tworzy pętli nieskończonej, ponieważ `StateHasChanged` jest wywoływana tylko wtedy, gdy `infoFromJs` jest `null` .</span><span class="sxs-lookup"><span data-stu-id="96355-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

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

<span data-ttu-id="96355-115">W powyższym przykładowym kodzie Podaj `setElementText` funkcję JavaScript wewnątrz `<head>` elementu `wwwroot/index.html` (Blazor webassembly) lub `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="96355-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="96355-116">Funkcja jest wywoływana z <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> i zwraca wartość:</span><span class="sxs-lookup"><span data-stu-id="96355-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="96355-117">Poprzedni przykład modyfikuje Document Object Model (DOM) bezpośrednio wyłącznie w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="96355-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="96355-118">Nie zaleca się bezpośredniej modyfikacji modelu DOM przy użyciu języka JavaScript w większości scenariuszy, ponieważ kod JavaScript może zakłócać śledzenie zmian Blazor.</span><span class="sxs-lookup"><span data-stu-id="96355-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
