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
Podczas Blazor gdy aplikacja Server jest prerendering, niektóre akcje, takie jak wywołanie javascript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało ustanowione. Składniki mogą wymagać renderowania inaczej podczas prerendered.

Aby opóźnić połączenia interop JavaScript do momentu nawiązania połączenia z przeglądarką, można użyć [zdarzenia cyklu życia składnika OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render). To zdarzenie jest wywoływane tylko po aplikacji jest w pełni renderowane i nawiązywanego połączenia klienta.

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

W przypadku poprzedniego przykładowego `setElementText` kodu podaj `<head>` funkcję JavaScript wewnątrzBlazor elementu *wwwroot/index.html* (WebAssembly) lub *Pages/_Host.cshtml* (Blazor Serwer). Funkcja jest wywoływana z `IJSRuntime.InvokeVoidAsync` i nie zwraca wartości:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> W poprzednim przykładzie modyfikuje model obiektu dokumentu (DOM) bezpośrednio tylko w celach demonstracyjnych. Bezpośrednie modyfikowanie dom z JavaScript nie jest zalecane w większości scenariuszy, ponieważ JavaScript może zakłócać Blazorśledzenia zmian.

Poniższy składnik pokazuje, jak używać javascript interop jako część logiki inicjowania składnika w sposób, który jest zgodny z prerendering. Składnik pokazuje, że możliwe jest wyzwolenie aktualizacji `OnAfterRenderAsync`renderowania od wewnątrz . Deweloper musi unikać tworzenia nieskończonej pętli w tym scenariuszu.

Gdzie `JSRuntime.InvokeAsync` jest `ElementRef` wywoływana, `OnAfterRenderAsync` jest używany tylko w i nie w dowolnej wcześniejszej metody cyklu życia, ponieważ nie ma żadnego elementu JavaScript, dopóki po renderowane składnika.

[StateHasChanged](xref:blazor/lifecycle#state-changes) jest wywoływana do rerender składnika z nowym stanie uzyskanych z wywołania interop JavaScript. Kod nie tworzy nieskończonej pętli, ponieważ `StateHasChanged` `infoFromJs` jest `null`wywoływana tylko wtedy, gdy jest .

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

W przypadku poprzedniego przykładowego `setElementText` kodu podaj `<head>` funkcję JavaScript wewnątrzBlazor elementu *wwwroot/index.html* (WebAssembly) lub *Pages/_Host.cshtml* (Blazor Serwer). Funkcja jest wywoływana z `IJSRuntime.InvokeAsync` i zwraca wartość:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> W poprzednim przykładzie modyfikuje model obiektu dokumentu (DOM) bezpośrednio tylko w celach demonstracyjnych. Bezpośrednie modyfikowanie dom z JavaScript nie jest zalecane w większości scenariuszy, ponieważ JavaScript może zakłócać Blazorśledzenia zmian.
