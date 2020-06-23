---
---
Gdy aplikacja serwera Blazor jest wstępnie renderowana, niektóre akcje, takie jak wywoływanie kodu JavaScript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało nawiązane. Składniki mogą być konieczne w różny sposób, gdy są wstępnie renderowane.

Aby opóźnić wywołania międzyoperacyjne języka JavaScript do momentu ustanowienia połączenia z przeglądarką, można użyć [zdarzenia cyklu życia składnika OnAfterRenderAsync](xref:blazor/components/lifecycle#after-component-render). To zdarzenie jest wywoływane tylko wtedy, gdy aplikacja jest w pełni renderowana, a połączenie z klientem zostanie nawiązane.

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

W powyższym przykładowym kodzie Podaj `setElementText` funkcję JavaScript wewnątrz `<head>` elementu `wwwroot/index.html` (Blazor webassembly) lub `Pages/_Host.cshtml` (Blazor Server). Funkcja jest wywoływana z <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> i nie zwraca wartości:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Poprzedni przykład modyfikuje Document Object Model (DOM) bezpośrednio wyłącznie w celach demonstracyjnych. Nie zaleca się bezpośredniej modyfikacji modelu DOM przy użyciu języka JavaScript w większości scenariuszy, ponieważ kod JavaScript może zakłócać śledzenie zmian Blazor.

Poniższy składnik pokazuje, jak używać międzyoperacyjności JavaScript jako części logiki inicjalizacji składnika w sposób, który jest zgodny z renderowaniem. Składnik pokazuje, że można wyzwolić aktualizację renderowania z wewnątrz <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> . Deweloper musi unikać tworzenia pętli nieskończonej w tym scenariuszu.

Gdzie <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> jest wywoływana, `ElementRef` jest używana tylko w, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> a nie w żadnej wcześniejszej metodzie cyklu życia, ponieważ nie istnieje element JavaScript do momentu renderowania składnika.

[StateHasChanged](xref:blazor/components/lifecycle#state-changes) jest wywoływana, aby przetworzyć składnik z nowym stanem uzyskanym z wywołania międzyoperacyjnego języka JavaScript. Kod nie tworzy pętli nieskończonej, ponieważ `StateHasChanged` jest wywoływana tylko wtedy, gdy `infoFromJs` jest `null` .

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

W powyższym przykładowym kodzie Podaj `setElementText` funkcję JavaScript wewnątrz `<head>` elementu `wwwroot/index.html` (Blazor webassembly) lub `Pages/_Host.cshtml` (Blazor Server). Funkcja jest wywoływana z <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> i zwraca wartość:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> Poprzedni przykład modyfikuje Document Object Model (DOM) bezpośrednio wyłącznie w celach demonstracyjnych. Nie zaleca się bezpośredniej modyfikacji modelu DOM przy użyciu języka JavaScript w większości scenariuszy, ponieważ kod JavaScript może zakłócać śledzenie zmian Blazor.
