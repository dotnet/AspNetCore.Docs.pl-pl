---
title: BlazorObsługa zdarzeń ASP.NET Core
author: guardrex
description: Dowiedz się więcej o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/event-handling
ms.openlocfilehash: 4c574a630176010ab30006b53c42e5cad7f93a54
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395191"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="34a39-103">BlazorObsługa zdarzeń ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34a39-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="34a39-104">Razor składniki zapewniają funkcje obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="34a39-104">Razor components provide event handling features.</span></span> <span data-ttu-id="34a39-105">Dla atrybutu elementu HTML o nazwie [`@on{EVENT}`](xref:mvc/views/razor#onevent) (na przykład `@onclick` ) z wartością typu delegata Razor składnik traktuje wartość atrybutu jako procedurę obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="34a39-105">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="34a39-106">Poniższy kod wywołuje metodę, `UpdateHeading` gdy przycisk zostanie wybrany w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="34a39-106">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="34a39-107">Poniższy kod wywołuje metodę, `CheckChanged` gdy pole wyboru zostanie zmienione w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="34a39-107">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="34a39-108">Procedury obsługi zdarzeń mogą również być asynchroniczne i zwracać <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="34a39-108">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="34a39-109">Nie ma potrzeby ręcznego wywoływania [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="34a39-109">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="34a39-110">Wyjątki są rejestrowane, gdy wystąpią.</span><span class="sxs-lookup"><span data-stu-id="34a39-110">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="34a39-111">W poniższym przykładzie `UpdateHeading` jest wywoływana asynchronicznie po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="34a39-111">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="34a39-112">Typy argumentów zdarzeń</span><span class="sxs-lookup"><span data-stu-id="34a39-112">Event argument types</span></span>

<span data-ttu-id="34a39-113">W przypadku niektórych zdarzeń dozwolone są typy argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="34a39-113">For some events, event argument types are permitted.</span></span> <span data-ttu-id="34a39-114">Określanie parametru zdarzenia w definicji metody zdarzenia jest opcjonalne i wymagane tylko wtedy, gdy typ zdarzenia jest używany w metodzie.</span><span class="sxs-lookup"><span data-stu-id="34a39-114">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="34a39-115">W poniższym przykładzie `MouseEventArgs` argument zdarzenia jest używany w `ShowMessage` metodzie do ustawiania tekstu komunikatu:</span><span class="sxs-lookup"><span data-stu-id="34a39-115">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="34a39-116">Obsługiwane <xref:System.EventArgs> są przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="34a39-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="34a39-117">Zdarzenie</span><span class="sxs-lookup"><span data-stu-id="34a39-117">Event</span></span>            | <span data-ttu-id="34a39-118">Klasa</span><span class="sxs-lookup"><span data-stu-id="34a39-118">Class</span></span>  | <span data-ttu-id="34a39-119">Zdarzenia i uwagi dotyczące modelu DOM</span><span class="sxs-lookup"><span data-stu-id="34a39-119">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="34a39-120">Schowek</span><span class="sxs-lookup"><span data-stu-id="34a39-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="34a39-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="34a39-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="34a39-122">Przeciągnąć</span><span class="sxs-lookup"><span data-stu-id="34a39-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="34a39-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="34a39-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="34a39-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> i <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> przytrzymaj przeciągane dane elementu.</span><span class="sxs-lookup"><span data-stu-id="34a39-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="34a39-125">Zaimplementuj przeciąganie i upuszczanie w Blazor aplikacjach przy użyciu usługi [js Interop](xref:blazor/call-javascript-from-dotnet) z [interfejsem API przeciągania i upuszczania w formacie HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="34a39-125">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="34a39-126">Error</span><span class="sxs-lookup"><span data-stu-id="34a39-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="34a39-127">Zdarzenie</span><span class="sxs-lookup"><span data-stu-id="34a39-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="34a39-128">*Ogólne*</span><span class="sxs-lookup"><span data-stu-id="34a39-128">*General*</span></span><br><span data-ttu-id="34a39-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="34a39-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="34a39-130">*Schowek*</span><span class="sxs-lookup"><span data-stu-id="34a39-130">*Clipboard*</span></span><br><span data-ttu-id="34a39-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="34a39-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="34a39-132">*Dane wejściowe*</span><span class="sxs-lookup"><span data-stu-id="34a39-132">*Input*</span></span><br><span data-ttu-id="34a39-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="34a39-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="34a39-134">*Media*</span><span class="sxs-lookup"><span data-stu-id="34a39-134">*Media*</span></span><br><span data-ttu-id="34a39-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="34a39-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="34a39-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> przechowuje atrybuty, aby skonfigurować mapowania między nazwami zdarzeń i typami argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="34a39-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="34a39-137">Ukierunkowanie</span><span class="sxs-lookup"><span data-stu-id="34a39-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="34a39-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="34a39-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="34a39-139">Nie obejmuje obsługi dla `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="34a39-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="34a39-140">Dane wejściowe</span><span class="sxs-lookup"><span data-stu-id="34a39-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="34a39-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="34a39-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="34a39-142">Klawiatura</span><span class="sxs-lookup"><span data-stu-id="34a39-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="34a39-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="34a39-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="34a39-144">Mysz</span><span class="sxs-lookup"><span data-stu-id="34a39-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="34a39-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="34a39-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="34a39-146">Wskaźnik myszy</span><span class="sxs-lookup"><span data-stu-id="34a39-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="34a39-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="34a39-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="34a39-148">Kółka myszy</span><span class="sxs-lookup"><span data-stu-id="34a39-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="34a39-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="34a39-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="34a39-150">Postęp</span><span class="sxs-lookup"><span data-stu-id="34a39-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="34a39-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="34a39-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="34a39-152">Dotyk</span><span class="sxs-lookup"><span data-stu-id="34a39-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="34a39-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="34a39-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="34a39-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> reprezentuje pojedynczy punkt kontaktu na urządzeniu z wrażliwym dotknięciem.</span><span class="sxs-lookup"><span data-stu-id="34a39-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="34a39-155">Zdarzenie</span><span class="sxs-lookup"><span data-stu-id="34a39-155">Event</span></span>            | <span data-ttu-id="34a39-156">Klasa</span><span class="sxs-lookup"><span data-stu-id="34a39-156">Class</span></span> | <span data-ttu-id="34a39-157">Zdarzenia i uwagi dotyczące modelu DOM</span><span class="sxs-lookup"><span data-stu-id="34a39-157">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="34a39-158">Schowek</span><span class="sxs-lookup"><span data-stu-id="34a39-158">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="34a39-159">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="34a39-159">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="34a39-160">Przeciągnąć</span><span class="sxs-lookup"><span data-stu-id="34a39-160">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="34a39-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="34a39-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="34a39-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> i <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> przytrzymaj przeciągane dane elementu.</span><span class="sxs-lookup"><span data-stu-id="34a39-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="34a39-163">Zaimplementuj przeciąganie i upuszczanie w Blazor aplikacjach przy użyciu usługi [js Interop](xref:blazor/call-javascript-from-dotnet) z [interfejsem API przeciągania i upuszczania w formacie HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="34a39-163">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="34a39-164">Error</span><span class="sxs-lookup"><span data-stu-id="34a39-164">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="34a39-165">Zdarzenie</span><span class="sxs-lookup"><span data-stu-id="34a39-165">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="34a39-166">*Ogólne*</span><span class="sxs-lookup"><span data-stu-id="34a39-166">*General*</span></span><br><span data-ttu-id="34a39-167">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="34a39-167">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="34a39-168">*Schowek*</span><span class="sxs-lookup"><span data-stu-id="34a39-168">*Clipboard*</span></span><br><span data-ttu-id="34a39-169">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="34a39-169">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="34a39-170">*Dane wejściowe*</span><span class="sxs-lookup"><span data-stu-id="34a39-170">*Input*</span></span><br><span data-ttu-id="34a39-171">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="34a39-171">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="34a39-172">*Media*</span><span class="sxs-lookup"><span data-stu-id="34a39-172">*Media*</span></span><br><span data-ttu-id="34a39-173">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="34a39-173">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="34a39-174"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> przechowuje atrybuty, aby skonfigurować mapowania między nazwami zdarzeń i typami argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="34a39-174"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="34a39-175">Ukierunkowanie</span><span class="sxs-lookup"><span data-stu-id="34a39-175">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="34a39-176">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="34a39-176">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="34a39-177">Nie obejmuje obsługi dla `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="34a39-177">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="34a39-178">Dane wejściowe</span><span class="sxs-lookup"><span data-stu-id="34a39-178">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="34a39-179">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="34a39-179">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="34a39-180">Klawiatura</span><span class="sxs-lookup"><span data-stu-id="34a39-180">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="34a39-181">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="34a39-181">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="34a39-182">Mysz</span><span class="sxs-lookup"><span data-stu-id="34a39-182">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="34a39-183">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="34a39-183">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="34a39-184">Wskaźnik myszy</span><span class="sxs-lookup"><span data-stu-id="34a39-184">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="34a39-185">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="34a39-185">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="34a39-186">Kółka myszy</span><span class="sxs-lookup"><span data-stu-id="34a39-186">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="34a39-187">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="34a39-187">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="34a39-188">Postęp</span><span class="sxs-lookup"><span data-stu-id="34a39-188">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="34a39-189">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="34a39-189">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="34a39-190">Dotyk</span><span class="sxs-lookup"><span data-stu-id="34a39-190">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="34a39-191">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="34a39-191">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="34a39-192"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> reprezentuje pojedynczy punkt kontaktu na urządzeniu z wrażliwym dotknięciem.</span><span class="sxs-lookup"><span data-stu-id="34a39-192"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="34a39-193">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="34a39-193">For more information, see the following resources:</span></span>

* [<span data-ttu-id="34a39-194">`EventArgs` klasy w źródle odwołania ASP.NET Core (gałąź dotnet/aspnetcore `main` )</span><span class="sxs-lookup"><span data-stu-id="34a39-194">`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `main` branch)</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Components/Web/src/Web)

  [!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

* <span data-ttu-id="34a39-195">[Powiadomienia MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): zawiera informacje o tym, które elementy HTML obsługują każde wydarzenie dom.</span><span class="sxs-lookup"><span data-stu-id="34a39-195">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="34a39-196">Wyrażenia lambda</span><span class="sxs-lookup"><span data-stu-id="34a39-196">Lambda expressions</span></span>

<span data-ttu-id="34a39-197">[Wyrażenia lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) mogą być również używane:</span><span class="sxs-lookup"><span data-stu-id="34a39-197">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="34a39-198">Często wygodnie jest blisko dodatkowych wartości, na przykład podczas iteracji na zestawie elementów.</span><span class="sxs-lookup"><span data-stu-id="34a39-198">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="34a39-199">Poniższy przykład tworzy trzy przyciski, z których każdy wywołuje `UpdateHeading` przekazanie argumentu zdarzenia ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) i jego numer przycisku ( `buttonNumber` ), po wybraniu w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="34a39-199">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="34a39-200">**Nie** używaj zmiennej Loop bezpośrednio w wyrażeniu lambda, na przykład `i` w powyższym `for` przykładzie pętli.</span><span class="sxs-lookup"><span data-stu-id="34a39-200">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="34a39-201">W przeciwnym razie ta sama zmienna jest używana przez wszystkie wyrażenia lambda, które w wyniku używają tej samej wartości we wszystkich lambdach.</span><span class="sxs-lookup"><span data-stu-id="34a39-201">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="34a39-202">Zawsze Przechwytuj wartość zmiennej w zmiennej lokalnej, a następnie użyj jej.</span><span class="sxs-lookup"><span data-stu-id="34a39-202">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="34a39-203">W poprzednim przykładzie zmienna Loop `i` jest przypisana do `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="34a39-203">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="34a39-204">EventCallback</span><span class="sxs-lookup"><span data-stu-id="34a39-204">EventCallback</span></span>

<span data-ttu-id="34a39-205">Typowym scenariuszem ze składnikami zagnieżdżonymi jest uruchomienie metody składnika nadrzędnego, gdy wystąpi zdarzenie składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="34a39-205">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="34a39-206">`onclick`Zdarzenie występujące w składniku podrzędnym jest wspólnym przypadkiem użycia.</span><span class="sxs-lookup"><span data-stu-id="34a39-206">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="34a39-207">Aby uwidocznić zdarzenia między składnikami, użyj <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="34a39-207">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="34a39-208">Składnik nadrzędny może przypisać metodę wywołania zwrotnego do składnika podrzędnego <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="34a39-208">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="34a39-209">`ChildComponent`W aplikacji przykładowej ( `Components/ChildComponent.razor` ) pokazano, jak `onclick` program obsługi przycisku został skonfigurowany tak, aby otrzymać <xref:Microsoft.AspNetCore.Components.EventCallback> delegata z przykładu `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="34a39-209">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="34a39-210"><xref:Microsoft.AspNetCore.Components.EventCallback>Typ ma wartość `MouseEventArgs` , która jest odpowiednia dla `onclick` zdarzenia z urządzenia peryferyjnego:</span><span class="sxs-lookup"><span data-stu-id="34a39-210">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="34a39-211">`ParentComponent`Ustawia element podrzędny <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) na jego `ShowMessage` metodę.</span><span class="sxs-lookup"><span data-stu-id="34a39-211">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="34a39-212">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="34a39-212">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="34a39-213">Gdy przycisk zostanie wybrany w `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="34a39-213">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="34a39-214">`ParentComponent` `ShowMessage` Metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="34a39-214">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="34a39-215">`messageText` jest aktualizowany i wyświetlany w `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="34a39-215">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="34a39-216">Wywołanie [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) nie jest wymagane w metodzie wywołania zwrotnego ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="34a39-216">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="34a39-217"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest automatycznie wywoływana w celu odrenderowania `ParentComponent` elementu, podobnie jak zdarzenia podrzędne wyzwala ponowne renderowanie składnika w obsłudze zdarzeń, które są wykonywane w ramach elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="34a39-217"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span> <span data-ttu-id="34a39-218">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="34a39-218">For more information, see <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="34a39-219"><xref:Microsoft.AspNetCore.Components.EventCallback> i <xref:Microsoft.AspNetCore.Components.EventCallback%601> Zezwalaj na asynchroniczne Delegaty.</span><span class="sxs-lookup"><span data-stu-id="34a39-219"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="34a39-220"><xref:Microsoft.AspNetCore.Components.EventCallback> jest słabo wpisywany i umożliwia przekazanie dowolnego argumentu typu w `InvokeAsync(Object)` .</span><span class="sxs-lookup"><span data-stu-id="34a39-220"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="34a39-221"><xref:Microsoft.AspNetCore.Components.EventCallback%601> jest silnie wpisana i wymaga przekazania `T` argumentu w elemencie `InvokeAsync(T)` , który można przypisać do `TValue` .</span><span class="sxs-lookup"><span data-stu-id="34a39-221"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="34a39-222">Wywołaj <xref:Microsoft.AspNetCore.Components.EventCallback> lub <xref:Microsoft.AspNetCore.Components.EventCallback%601> z <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> i await <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="34a39-222">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="34a39-223">Używaj <xref:Microsoft.AspNetCore.Components.EventCallback> i <xref:Microsoft.AspNetCore.Components.EventCallback%601> dla parametrów składnika Obsługa zdarzeń i powiązania.</span><span class="sxs-lookup"><span data-stu-id="34a39-223">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="34a39-224">Preferuj silnie wpisaną <xref:Microsoft.AspNetCore.Components.EventCallback%601> wartość <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="34a39-224">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="34a39-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> zapewnia lepszą opinię o błędach dla użytkowników składnika.</span><span class="sxs-lookup"><span data-stu-id="34a39-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="34a39-226">Podobnie jak w przypadku innych programów obsługi zdarzeń interfejsu użytkownika, określenie parametru zdarzenia jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="34a39-226">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="34a39-227">Użyj <xref:Microsoft.AspNetCore.Components.EventCallback> w przypadku braku wartości przekazywania do wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="34a39-227">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="34a39-228">Zapobiegaj akcjom domyślnym</span><span class="sxs-lookup"><span data-stu-id="34a39-228">Prevent default actions</span></span>

<span data-ttu-id="34a39-229">Użyj [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atrybutu dyrektywy, aby zapobiec domyślnej akcji dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="34a39-229">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="34a39-230">Po wybraniu klucza na urządzeniu wejściowym, gdy fokus elementu znajduje się w polu tekstowym, przeglądarka zwykle wyświetla znak klucza w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="34a39-230">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="34a39-231">W poniższym przykładzie zachowanie domyślne jest blokowane przez określenie `@onkeypress:preventDefault` atrybutu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="34a39-231">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="34a39-232">Licznik jest zwiększany, a **+** klucz nie jest przechwytywany do `<input>` wartości elementu:</span><span class="sxs-lookup"><span data-stu-id="34a39-232">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="34a39-233">Określanie `@on{EVENT}:preventDefault` atrybutu bez wartości jest równoważne `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="34a39-233">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="34a39-234">Wartość atrybutu może również być wyrażeniem.</span><span class="sxs-lookup"><span data-stu-id="34a39-234">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="34a39-235">W poniższym przykładzie `shouldPreventDefault` `bool` pole jest ustawione na jedną `true` lub `false` :</span><span class="sxs-lookup"><span data-stu-id="34a39-235">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="34a39-236">Zatrzymaj propagację zdarzeń</span><span class="sxs-lookup"><span data-stu-id="34a39-236">Stop event propagation</span></span>

<span data-ttu-id="34a39-237">Użyj [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atrybutu dyrektywy, aby zatrzymać propagację zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="34a39-237">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="34a39-238">W poniższym przykładzie, zaznaczając pole wyboru, uniemożliwiają klikanie zdarzeń z drugiego elementu podrzędnego `<div>` od propagowania do obiektu nadrzędnego `<div>` :</span><span class="sxs-lookup"><span data-stu-id="34a39-238">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

## <a name="focus-an-element"></a><span data-ttu-id="34a39-239">Skupienie się na elemencie</span><span class="sxs-lookup"><span data-stu-id="34a39-239">Focus an element</span></span>

<span data-ttu-id="34a39-240">Wywołaj `FocusAsync` [odwołanie do elementu](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) , aby skoncentrować się na elemencie w kodzie:</span><span class="sxs-lookup"><span data-stu-id="34a39-240">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

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
