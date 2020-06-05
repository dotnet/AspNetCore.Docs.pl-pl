---
title: BlazorObsługa zdarzeń ASP.NET Core
author: guardrex
description: Dowiedz się więcej o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 2d806413eb66d4c1301df4a0db950e8b919c09a4
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84419752"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="934da-103">BlazorObsługa zdarzeń ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="934da-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="934da-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="934da-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="934da-105">składniki zapewniają funkcje obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="934da-105"> components provide event handling features.</span></span> <span data-ttu-id="934da-106">Dla atrybutu elementu HTML o nazwie [`@on{EVENT}`](xref:mvc/views/razor#onevent) (na przykład `@onclick` ) z wartością typu delegata Razor składnik traktuje wartość atrybutu jako procedurę obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="934da-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="934da-107">Poniższy kod wywołuje metodę, `UpdateHeading` gdy przycisk zostanie wybrany w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="934da-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="934da-108">Poniższy kod wywołuje metodę, `CheckChanged` gdy pole wyboru zostanie zmienione w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="934da-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="934da-109">Procedury obsługi zdarzeń mogą również być asynchroniczne i zwracać <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="934da-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="934da-110">Nie ma potrzeby ręcznego wywoływania [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="934da-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="934da-111">Wyjątki są rejestrowane, gdy wystąpią.</span><span class="sxs-lookup"><span data-stu-id="934da-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="934da-112">W poniższym przykładzie `UpdateHeading` jest wywoływana asynchronicznie po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="934da-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="934da-113">Typy argumentów zdarzeń</span><span class="sxs-lookup"><span data-stu-id="934da-113">Event argument types</span></span>

<span data-ttu-id="934da-114">W przypadku niektórych zdarzeń dozwolone są typy argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="934da-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="934da-115">Określanie typu zdarzenia w wywołaniu metody jest konieczne tylko wtedy, gdy typ zdarzenia jest używany w metodzie.</span><span class="sxs-lookup"><span data-stu-id="934da-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="934da-116">Obsługiwane <xref:System.EventArgs> są przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="934da-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="934da-117">Wydarzenie</span><span class="sxs-lookup"><span data-stu-id="934da-117">Event</span></span>            | <span data-ttu-id="934da-118">Klasa</span><span class="sxs-lookup"><span data-stu-id="934da-118">Class</span></span>                | <span data-ttu-id="934da-119">Zdarzenia i uwagi dotyczące modelu DOM</span><span class="sxs-lookup"><span data-stu-id="934da-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="934da-120">Schowek</span><span class="sxs-lookup"><span data-stu-id="934da-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="934da-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="934da-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="934da-122">Przeciągnąć</span><span class="sxs-lookup"><span data-stu-id="934da-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="934da-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="934da-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="934da-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>i <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> przytrzymaj przeciągane dane elementu.</span><span class="sxs-lookup"><span data-stu-id="934da-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="934da-125">Błąd</span><span class="sxs-lookup"><span data-stu-id="934da-125">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="934da-126">Wydarzenie</span><span class="sxs-lookup"><span data-stu-id="934da-126">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="934da-127">*Ogólne*</span><span class="sxs-lookup"><span data-stu-id="934da-127">*General*</span></span><br><span data-ttu-id="934da-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="934da-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="934da-129">*Schowek*</span><span class="sxs-lookup"><span data-stu-id="934da-129">*Clipboard*</span></span><br><span data-ttu-id="934da-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="934da-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="934da-131">*Dane wejściowe*</span><span class="sxs-lookup"><span data-stu-id="934da-131">*Input*</span></span><br><span data-ttu-id="934da-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="934da-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="934da-133">*Media*</span><span class="sxs-lookup"><span data-stu-id="934da-133">*Media*</span></span><br><span data-ttu-id="934da-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="934da-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="934da-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>przechowuje atrybuty, aby skonfigurować mapowania między nazwami zdarzeń i typami argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="934da-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="934da-136">Fokus</span><span class="sxs-lookup"><span data-stu-id="934da-136">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="934da-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="934da-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="934da-138">Nie obejmuje obsługi dla `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="934da-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="934da-139">Dane wejściowe</span><span class="sxs-lookup"><span data-stu-id="934da-139">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="934da-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="934da-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="934da-141">Klawiatura</span><span class="sxs-lookup"><span data-stu-id="934da-141">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="934da-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="934da-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="934da-143">Mysz</span><span class="sxs-lookup"><span data-stu-id="934da-143">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="934da-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="934da-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="934da-145">Wskaźnik myszy</span><span class="sxs-lookup"><span data-stu-id="934da-145">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="934da-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="934da-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="934da-147">Kółka myszy</span><span class="sxs-lookup"><span data-stu-id="934da-147">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="934da-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="934da-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="934da-149">Postęp</span><span class="sxs-lookup"><span data-stu-id="934da-149">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="934da-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="934da-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="934da-151">Dotyk</span><span class="sxs-lookup"><span data-stu-id="934da-151">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="934da-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="934da-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="934da-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>reprezentuje pojedynczy punkt kontaktu na urządzeniu z wrażliwym dotknięciem.</span><span class="sxs-lookup"><span data-stu-id="934da-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="934da-154">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="934da-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="934da-155">[Klasy EventArgs w źródle odwołania ASP.NET Core (gałąź dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="934da-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="934da-156">[Powiadomienia MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): zawiera informacje o tym, które elementy HTML obsługują każde wydarzenie dom.</span><span class="sxs-lookup"><span data-stu-id="934da-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="934da-157">Wyrażenia lambda</span><span class="sxs-lookup"><span data-stu-id="934da-157">Lambda expressions</span></span>

<span data-ttu-id="934da-158">[Wyrażenia lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) mogą być również używane:</span><span class="sxs-lookup"><span data-stu-id="934da-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="934da-159">Często wygodnie jest blisko dodatkowych wartości, na przykład podczas iteracji na zestawie elementów.</span><span class="sxs-lookup"><span data-stu-id="934da-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="934da-160">Poniższy przykład tworzy trzy przyciski, z których każdy wywołuje `UpdateHeading` przekazanie argumentu zdarzenia ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) i jego numer przycisku ( `buttonNumber` ), po wybraniu w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="934da-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="934da-161">**Nie** używaj zmiennej Loop bezpośrednio w wyrażeniu lambda, na przykład `i` w powyższym `for` przykładzie pętli lub zmiennej odwołania w `foreach` pętli.</span><span class="sxs-lookup"><span data-stu-id="934da-161">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="934da-162">W przeciwnym razie ta sama zmienna jest używana przez wszystkie wyrażenia lambda, które w wyniku używają tej samej wartości we wszystkich lambdach.</span><span class="sxs-lookup"><span data-stu-id="934da-162">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="934da-163">Zawsze Przechwytuj wartość zmiennej w zmiennej lokalnej, a następnie użyj jej.</span><span class="sxs-lookup"><span data-stu-id="934da-163">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="934da-164">W poprzednim przykładzie zmienna Loop `i` jest przypisana do `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="934da-164">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="934da-165">EventCallback</span><span class="sxs-lookup"><span data-stu-id="934da-165">EventCallback</span></span>

<span data-ttu-id="934da-166">Typowym scenariuszem ze składnikami zagnieżdżonymi jest uruchomienie metody składnika nadrzędnego, gdy wystąpi zdarzenie składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="934da-166">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="934da-167">`onclick`Zdarzenie występujące w składniku podrzędnym jest wspólnym przypadkiem użycia.</span><span class="sxs-lookup"><span data-stu-id="934da-167">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="934da-168">Aby uwidocznić zdarzenia między składnikami, użyj <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="934da-168">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="934da-169">Składnik nadrzędny może przypisać metodę wywołania zwrotnego do składnika podrzędnego <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="934da-169">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="934da-170">`ChildComponent`W aplikacji przykładowej (*Components/ChildComponent. Razor*) pokazano, jak `onclick` program obsługi przycisku został skonfigurowany tak, aby otrzymać <xref:Microsoft.AspNetCore.Components.EventCallback> delegata z przykładu `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="934da-170">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="934da-171"><xref:Microsoft.AspNetCore.Components.EventCallback>Typ ma wartość `MouseEventArgs` , która jest odpowiednia dla `onclick` zdarzenia z urządzenia peryferyjnego:</span><span class="sxs-lookup"><span data-stu-id="934da-171">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="934da-172">`ParentComponent`Ustawia element podrzędny <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) na jego `ShowMessage` metodę.</span><span class="sxs-lookup"><span data-stu-id="934da-172">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="934da-173">*Strony/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="934da-173">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="934da-174">Gdy przycisk zostanie wybrany w `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="934da-174">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="934da-175">`ParentComponent` `ShowMessage` Metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="934da-175">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="934da-176">`messageText`jest aktualizowany i wyświetlany w `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="934da-176">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="934da-177">Wywołanie [StateHasChanged](xref:blazor/lifecycle#state-changes) nie jest wymagane w metodzie wywołania zwrotnego ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="934da-177">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="934da-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>jest automatycznie wywoływana w celu odrenderowania `ParentComponent` elementu, podobnie jak zdarzenia podrzędne wyzwala ponowne renderowanie składnika w obsłudze zdarzeń, które są wykonywane w ramach elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="934da-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="934da-179"><xref:Microsoft.AspNetCore.Components.EventCallback>i <xref:Microsoft.AspNetCore.Components.EventCallback%601> Zezwalaj na asynchroniczne Delegaty.</span><span class="sxs-lookup"><span data-stu-id="934da-179"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="934da-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601>jest silnie wpisana i wymaga określonego typu argumentu.</span><span class="sxs-lookup"><span data-stu-id="934da-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="934da-181"><xref:Microsoft.AspNetCore.Components.EventCallback>jest słabo wpisywany i zezwala na dowolny typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="934da-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="934da-182">Wywołaj <xref:Microsoft.AspNetCore.Components.EventCallback> lub <xref:Microsoft.AspNetCore.Components.EventCallback%601> z <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> i await <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="934da-182">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="934da-183">Używaj <xref:Microsoft.AspNetCore.Components.EventCallback> i <xref:Microsoft.AspNetCore.Components.EventCallback%601> dla parametrów składnika Obsługa zdarzeń i powiązania.</span><span class="sxs-lookup"><span data-stu-id="934da-183">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="934da-184">Preferuj silnie wpisaną <xref:Microsoft.AspNetCore.Components.EventCallback%601> wartość <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="934da-184">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="934da-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601>zapewnia lepszą opinię o błędach dla użytkowników składnika.</span><span class="sxs-lookup"><span data-stu-id="934da-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="934da-186">Podobnie jak w przypadku innych programów obsługi zdarzeń interfejsu użytkownika, określenie parametru zdarzenia jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="934da-186">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="934da-187">Użyj <xref:Microsoft.AspNetCore.Components.EventCallback> w przypadku braku wartości przekazywania do wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="934da-187">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="934da-188">Zapobiegaj akcjom domyślnym</span><span class="sxs-lookup"><span data-stu-id="934da-188">Prevent default actions</span></span>

<span data-ttu-id="934da-189">Użyj [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atrybutu dyrektywy, aby zapobiec domyślnej akcji dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="934da-189">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="934da-190">Po wybraniu klucza na urządzeniu wejściowym, gdy fokus elementu znajduje się w polu tekstowym, przeglądarka zwykle wyświetla znak klucza w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="934da-190">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="934da-191">W poniższym przykładzie zachowanie domyślne jest blokowane przez określenie `@onkeypress:preventDefault` atrybutu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="934da-191">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="934da-192">Licznik jest zwiększany, a **+** klucz nie jest przechwytywany do `<input>` wartości elementu:</span><span class="sxs-lookup"><span data-stu-id="934da-192">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="934da-193">Określanie `@on{EVENT}:preventDefault` atrybutu bez wartości jest równoważne `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="934da-193">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="934da-194">Wartość atrybutu może również być wyrażeniem.</span><span class="sxs-lookup"><span data-stu-id="934da-194">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="934da-195">W poniższym przykładzie `shouldPreventDefault` `bool` pole jest ustawione na jedną `true` lub `false` :</span><span class="sxs-lookup"><span data-stu-id="934da-195">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="934da-196">Procedura obsługi zdarzeń nie jest wymagana, aby zapobiec akcji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="934da-196">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="934da-197">Procedury obsługi zdarzeń i zapobiegania domyślnym scenariuszom akcji mogą być używane niezależnie.</span><span class="sxs-lookup"><span data-stu-id="934da-197">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="934da-198">Zatrzymaj propagację zdarzeń</span><span class="sxs-lookup"><span data-stu-id="934da-198">Stop event propagation</span></span>

<span data-ttu-id="934da-199">Użyj [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atrybutu dyrektywy, aby zatrzymać propagację zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="934da-199">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="934da-200">W poniższym przykładzie, zaznaczając pole wyboru, uniemożliwiają klikanie zdarzeń z drugiego elementu podrzędnego `<div>` od propagowania do obiektu nadrzędnego `<div>` :</span><span class="sxs-lookup"><span data-stu-id="934da-200">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
