---
title: BlazorObsługa zdarzeń ASP.NET Core
author: guardrex
description: 'Dowiedz się więcej o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/event-handling
ms.openlocfilehash: d17547e7fb8a628a4864209d9857ac828f77c701
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056286"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="a26a1-103">BlazorObsługa zdarzeń ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a26a1-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="a26a1-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a26a1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a26a1-105">Razor składniki zapewniają funkcje obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a26a1-105">Razor components provide event handling features.</span></span> <span data-ttu-id="a26a1-106">Dla atrybutu elementu HTML o nazwie [`@on{EVENT}`](xref:mvc/views/razor#onevent) (na przykład `@onclick` ) z wartością typu delegata Razor składnik traktuje wartość atrybutu jako procedurę obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a26a1-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="a26a1-107">Poniższy kod wywołuje metodę, `UpdateHeading` gdy przycisk zostanie wybrany w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="a26a1-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="a26a1-108">Poniższy kod wywołuje metodę, `CheckChanged` gdy pole wyboru zostanie zmienione w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="a26a1-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="a26a1-109">Procedury obsługi zdarzeń mogą również być asynchroniczne i zwracać <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="a26a1-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="a26a1-110">Nie ma potrzeby ręcznego wywoływania [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="a26a1-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="a26a1-111">Wyjątki są rejestrowane, gdy wystąpią.</span><span class="sxs-lookup"><span data-stu-id="a26a1-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="a26a1-112">W poniższym przykładzie `UpdateHeading` jest wywoływana asynchronicznie po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="a26a1-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="a26a1-113">Typy argumentów zdarzeń</span><span class="sxs-lookup"><span data-stu-id="a26a1-113">Event argument types</span></span>

<span data-ttu-id="a26a1-114">W przypadku niektórych zdarzeń dozwolone są typy argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a26a1-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="a26a1-115">Określanie parametru zdarzenia w definicji metody zdarzenia jest opcjonalne i wymagane tylko wtedy, gdy typ zdarzenia jest używany w metodzie.</span><span class="sxs-lookup"><span data-stu-id="a26a1-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="a26a1-116">W poniższym przykładzie `MouseEventArgs` argument zdarzenia jest używany w `ShowMessage` metodzie do ustawiania tekstu komunikatu:</span><span class="sxs-lookup"><span data-stu-id="a26a1-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="a26a1-117">Obsługiwane <xref:System.EventArgs> są przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a26a1-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="a26a1-118">Zdarzenie</span><span class="sxs-lookup"><span data-stu-id="a26a1-118">Event</span></span>            | <span data-ttu-id="a26a1-119">Klasa</span><span class="sxs-lookup"><span data-stu-id="a26a1-119">Class</span></span>  | <span data-ttu-id="a26a1-120">Zdarzenia i uwagi dotyczące modelu DOM</span><span class="sxs-lookup"><span data-stu-id="a26a1-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="a26a1-121">Schowek</span><span class="sxs-lookup"><span data-stu-id="a26a1-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="a26a1-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="a26a1-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="a26a1-123">Przeciągnąć</span><span class="sxs-lookup"><span data-stu-id="a26a1-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="a26a1-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="a26a1-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="a26a1-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> i <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> przytrzymaj przeciągane dane elementu.</span><span class="sxs-lookup"><span data-stu-id="a26a1-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="a26a1-126">Zaimplementuj przeciąganie i upuszczanie w Blazor aplikacjach przy użyciu usługi [js Interop](xref:blazor/call-javascript-from-dotnet) z [interfejsem API przeciągania i upuszczania w formacie HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="a26a1-126">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="a26a1-127">Błąd</span><span class="sxs-lookup"><span data-stu-id="a26a1-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="a26a1-128">Zdarzenie</span><span class="sxs-lookup"><span data-stu-id="a26a1-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="a26a1-129">*Ogólne*</span><span class="sxs-lookup"><span data-stu-id="a26a1-129">*General*</span></span><br><span data-ttu-id="a26a1-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="a26a1-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="a26a1-131">*Schowek*</span><span class="sxs-lookup"><span data-stu-id="a26a1-131">*Clipboard*</span></span><br><span data-ttu-id="a26a1-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="a26a1-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="a26a1-133">*Dane wejściowe*</span><span class="sxs-lookup"><span data-stu-id="a26a1-133">*Input*</span></span><br><span data-ttu-id="a26a1-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="a26a1-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="a26a1-135">*Media*</span><span class="sxs-lookup"><span data-stu-id="a26a1-135">*Media*</span></span><br><span data-ttu-id="a26a1-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="a26a1-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="a26a1-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> przechowuje atrybuty, aby skonfigurować mapowania między nazwami zdarzeń i typami argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a26a1-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="a26a1-138">Ukierunkowanie</span><span class="sxs-lookup"><span data-stu-id="a26a1-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="a26a1-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="a26a1-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="a26a1-140">Nie obejmuje obsługi dla `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="a26a1-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="a26a1-141">Dane wejściowe</span><span class="sxs-lookup"><span data-stu-id="a26a1-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="a26a1-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="a26a1-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="a26a1-143">Klawiatura</span><span class="sxs-lookup"><span data-stu-id="a26a1-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="a26a1-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="a26a1-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="a26a1-145">Mysz</span><span class="sxs-lookup"><span data-stu-id="a26a1-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="a26a1-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="a26a1-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="a26a1-147">Wskaźnik myszy</span><span class="sxs-lookup"><span data-stu-id="a26a1-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="a26a1-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="a26a1-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="a26a1-149">Kółka myszy</span><span class="sxs-lookup"><span data-stu-id="a26a1-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="a26a1-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="a26a1-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="a26a1-151">Postęp</span><span class="sxs-lookup"><span data-stu-id="a26a1-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="a26a1-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="a26a1-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="a26a1-153">Dotyk</span><span class="sxs-lookup"><span data-stu-id="a26a1-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="a26a1-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="a26a1-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="a26a1-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> reprezentuje pojedynczy punkt kontaktu na urządzeniu z wrażliwym dotknięciem.</span><span class="sxs-lookup"><span data-stu-id="a26a1-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="a26a1-156">Zdarzenie</span><span class="sxs-lookup"><span data-stu-id="a26a1-156">Event</span></span>            | <span data-ttu-id="a26a1-157">Klasa</span><span class="sxs-lookup"><span data-stu-id="a26a1-157">Class</span></span> | <span data-ttu-id="a26a1-158">Zdarzenia i uwagi dotyczące modelu DOM</span><span class="sxs-lookup"><span data-stu-id="a26a1-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="a26a1-159">Schowek</span><span class="sxs-lookup"><span data-stu-id="a26a1-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="a26a1-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="a26a1-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="a26a1-161">Przeciągnąć</span><span class="sxs-lookup"><span data-stu-id="a26a1-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="a26a1-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="a26a1-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="a26a1-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> i <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> przytrzymaj przeciągane dane elementu.</span><span class="sxs-lookup"><span data-stu-id="a26a1-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="a26a1-164">Zaimplementuj przeciąganie i upuszczanie w Blazor aplikacjach przy użyciu usługi [js Interop](xref:blazor/call-javascript-from-dotnet) z [interfejsem API przeciągania i upuszczania w formacie HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="a26a1-164">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="a26a1-165">Błąd</span><span class="sxs-lookup"><span data-stu-id="a26a1-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="a26a1-166">Zdarzenie</span><span class="sxs-lookup"><span data-stu-id="a26a1-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="a26a1-167">*Ogólne*</span><span class="sxs-lookup"><span data-stu-id="a26a1-167">*General*</span></span><br><span data-ttu-id="a26a1-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="a26a1-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="a26a1-169">*Schowek*</span><span class="sxs-lookup"><span data-stu-id="a26a1-169">*Clipboard*</span></span><br><span data-ttu-id="a26a1-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="a26a1-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="a26a1-171">*Dane wejściowe*</span><span class="sxs-lookup"><span data-stu-id="a26a1-171">*Input*</span></span><br><span data-ttu-id="a26a1-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="a26a1-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="a26a1-173">*Media*</span><span class="sxs-lookup"><span data-stu-id="a26a1-173">*Media*</span></span><br><span data-ttu-id="a26a1-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="a26a1-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="a26a1-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> przechowuje atrybuty, aby skonfigurować mapowania między nazwami zdarzeń i typami argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a26a1-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="a26a1-176">Ukierunkowanie</span><span class="sxs-lookup"><span data-stu-id="a26a1-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="a26a1-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="a26a1-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="a26a1-178">Nie obejmuje obsługi dla `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="a26a1-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="a26a1-179">Dane wejściowe</span><span class="sxs-lookup"><span data-stu-id="a26a1-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="a26a1-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="a26a1-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="a26a1-181">Klawiatura</span><span class="sxs-lookup"><span data-stu-id="a26a1-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="a26a1-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="a26a1-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="a26a1-183">Mysz</span><span class="sxs-lookup"><span data-stu-id="a26a1-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="a26a1-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="a26a1-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="a26a1-185">Wskaźnik myszy</span><span class="sxs-lookup"><span data-stu-id="a26a1-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="a26a1-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="a26a1-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="a26a1-187">Kółka myszy</span><span class="sxs-lookup"><span data-stu-id="a26a1-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="a26a1-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="a26a1-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="a26a1-189">Postęp</span><span class="sxs-lookup"><span data-stu-id="a26a1-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="a26a1-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="a26a1-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="a26a1-191">Dotyk</span><span class="sxs-lookup"><span data-stu-id="a26a1-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="a26a1-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="a26a1-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="a26a1-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> reprezentuje pojedynczy punkt kontaktu na urządzeniu z wrażliwym dotknięciem.</span><span class="sxs-lookup"><span data-stu-id="a26a1-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="a26a1-194">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="a26a1-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="a26a1-195">[ `EventArgs` klasy w źródle odwołania ASP.NET Core (gałąź dotnet/aspnetcore `master` )](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="a26a1-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="a26a1-196">`master`Gałąź reprezentuje interfejs API w środowisku deweloperskim dla *następnej* wersji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a26a1-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="a26a1-197">Dla bieżącej wersji wybierz odpowiednią gałąź repozytorium GitHub (na przykład `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="a26a1-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="a26a1-198">[Powiadomienia MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): zawiera informacje o tym, które elementy HTML obsługują każde wydarzenie dom.</span><span class="sxs-lookup"><span data-stu-id="a26a1-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="a26a1-199">Wyrażenia lambda</span><span class="sxs-lookup"><span data-stu-id="a26a1-199">Lambda expressions</span></span>

<span data-ttu-id="a26a1-200">[Wyrażenia lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) mogą być również używane:</span><span class="sxs-lookup"><span data-stu-id="a26a1-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="a26a1-201">Często wygodnie jest blisko dodatkowych wartości, na przykład podczas iteracji na zestawie elementów.</span><span class="sxs-lookup"><span data-stu-id="a26a1-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="a26a1-202">Poniższy przykład tworzy trzy przyciski, z których każdy wywołuje `UpdateHeading` przekazanie argumentu zdarzenia ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) i jego numer przycisku ( `buttonNumber` ), po wybraniu w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="a26a1-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="a26a1-203">**Nie** używaj zmiennej Loop bezpośrednio w wyrażeniu lambda, na przykład `i` w powyższym `for` przykładzie pętli.</span><span class="sxs-lookup"><span data-stu-id="a26a1-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="a26a1-204">W przeciwnym razie ta sama zmienna jest używana przez wszystkie wyrażenia lambda, które w wyniku używają tej samej wartości we wszystkich lambdach.</span><span class="sxs-lookup"><span data-stu-id="a26a1-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="a26a1-205">Zawsze Przechwytuj wartość zmiennej w zmiennej lokalnej, a następnie użyj jej.</span><span class="sxs-lookup"><span data-stu-id="a26a1-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="a26a1-206">W poprzednim przykładzie zmienna Loop `i` jest przypisana do `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="a26a1-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="a26a1-207">EventCallback</span><span class="sxs-lookup"><span data-stu-id="a26a1-207">EventCallback</span></span>

<span data-ttu-id="a26a1-208">Typowym scenariuszem ze składnikami zagnieżdżonymi jest uruchomienie metody składnika nadrzędnego, gdy wystąpi zdarzenie składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="a26a1-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="a26a1-209">`onclick`Zdarzenie występujące w składniku podrzędnym jest wspólnym przypadkiem użycia.</span><span class="sxs-lookup"><span data-stu-id="a26a1-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="a26a1-210">Aby uwidocznić zdarzenia między składnikami, użyj <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="a26a1-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="a26a1-211">Składnik nadrzędny może przypisać metodę wywołania zwrotnego do składnika podrzędnego <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="a26a1-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="a26a1-212">`ChildComponent`W aplikacji przykładowej ( `Components/ChildComponent.razor` ) pokazano, jak `onclick` program obsługi przycisku został skonfigurowany tak, aby otrzymać <xref:Microsoft.AspNetCore.Components.EventCallback> delegata z przykładu `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="a26a1-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="a26a1-213"><xref:Microsoft.AspNetCore.Components.EventCallback>Typ ma wartość `MouseEventArgs` , która jest odpowiednia dla `onclick` zdarzenia z urządzenia peryferyjnego:</span><span class="sxs-lookup"><span data-stu-id="a26a1-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="a26a1-214">`ParentComponent`Ustawia element podrzędny <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) na jego `ShowMessage` metodę.</span><span class="sxs-lookup"><span data-stu-id="a26a1-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="a26a1-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="a26a1-215">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="a26a1-216">Gdy przycisk zostanie wybrany w `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="a26a1-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="a26a1-217">`ParentComponent` `ShowMessage` Metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="a26a1-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="a26a1-218">`messageText` jest aktualizowany i wyświetlany w `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="a26a1-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="a26a1-219">Wywołanie [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) nie jest wymagane w metodzie wywołania zwrotnego ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="a26a1-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="a26a1-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest automatycznie wywoływana w celu odrenderowania `ParentComponent` elementu, podobnie jak zdarzenia podrzędne wyzwala ponowne renderowanie składnika w obsłudze zdarzeń, które są wykonywane w ramach elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="a26a1-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="a26a1-221"><xref:Microsoft.AspNetCore.Components.EventCallback> i <xref:Microsoft.AspNetCore.Components.EventCallback%601> Zezwalaj na asynchroniczne Delegaty.</span><span class="sxs-lookup"><span data-stu-id="a26a1-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="a26a1-222"><xref:Microsoft.AspNetCore.Components.EventCallback> jest słabo wpisywany i umożliwia przekazanie dowolnego argumentu typu w `InvokeAsync(Object)` .</span><span class="sxs-lookup"><span data-stu-id="a26a1-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="a26a1-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> jest silnie wpisana i wymaga przekazania `T` argumentu w elemencie `InvokeAsync(T)` , który można przypisać do `TValue` .</span><span class="sxs-lookup"><span data-stu-id="a26a1-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="a26a1-224">Wywołaj <xref:Microsoft.AspNetCore.Components.EventCallback> lub <xref:Microsoft.AspNetCore.Components.EventCallback%601> z <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> i await <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="a26a1-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="a26a1-225">Używaj <xref:Microsoft.AspNetCore.Components.EventCallback> i <xref:Microsoft.AspNetCore.Components.EventCallback%601> dla parametrów składnika Obsługa zdarzeń i powiązania.</span><span class="sxs-lookup"><span data-stu-id="a26a1-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="a26a1-226">Preferuj silnie wpisaną <xref:Microsoft.AspNetCore.Components.EventCallback%601> wartość <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="a26a1-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="a26a1-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> zapewnia lepszą opinię o błędach dla użytkowników składnika.</span><span class="sxs-lookup"><span data-stu-id="a26a1-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="a26a1-228">Podobnie jak w przypadku innych programów obsługi zdarzeń interfejsu użytkownika, określenie parametru zdarzenia jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="a26a1-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="a26a1-229">Użyj <xref:Microsoft.AspNetCore.Components.EventCallback> w przypadku braku wartości przekazywania do wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="a26a1-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="a26a1-230">Zapobiegaj akcjom domyślnym</span><span class="sxs-lookup"><span data-stu-id="a26a1-230">Prevent default actions</span></span>

<span data-ttu-id="a26a1-231">Użyj [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atrybutu dyrektywy, aby zapobiec domyślnej akcji dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="a26a1-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="a26a1-232">Po wybraniu klucza na urządzeniu wejściowym, gdy fokus elementu znajduje się w polu tekstowym, przeglądarka zwykle wyświetla znak klucza w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="a26a1-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="a26a1-233">W poniższym przykładzie zachowanie domyślne jest blokowane przez określenie `@onkeypress:preventDefault` atrybutu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="a26a1-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="a26a1-234">Licznik jest zwiększany, a **+** klucz nie jest przechwytywany do `<input>` wartości elementu:</span><span class="sxs-lookup"><span data-stu-id="a26a1-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="a26a1-235">Określanie `@on{EVENT}:preventDefault` atrybutu bez wartości jest równoważne `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="a26a1-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="a26a1-236">Wartość atrybutu może również być wyrażeniem.</span><span class="sxs-lookup"><span data-stu-id="a26a1-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="a26a1-237">W poniższym przykładzie `shouldPreventDefault` `bool` pole jest ustawione na jedną `true` lub `false` :</span><span class="sxs-lookup"><span data-stu-id="a26a1-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="a26a1-238">Zatrzymaj propagację zdarzeń</span><span class="sxs-lookup"><span data-stu-id="a26a1-238">Stop event propagation</span></span>

<span data-ttu-id="a26a1-239">Użyj [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atrybutu dyrektywy, aby zatrzymać propagację zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a26a1-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="a26a1-240">W poniższym przykładzie, zaznaczając pole wyboru, uniemożliwiają klikanie zdarzeń z drugiego elementu podrzędnego `<div>` od propagowania do obiektu nadrzędnego `<div>` :</span><span class="sxs-lookup"><span data-stu-id="a26a1-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a><span data-ttu-id="a26a1-241">Skupienie się na elemencie</span><span class="sxs-lookup"><span data-stu-id="a26a1-241">Focus an element</span></span>

<span data-ttu-id="a26a1-242">Wywołaj `FocusAsync` [odwołanie do elementu](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) , aby skoncentrować się na elemencie w kodzie:</span><span class="sxs-lookup"><span data-stu-id="a26a1-242">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
