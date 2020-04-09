---
title: ASP.NET Obsługa zdarzeń Blazor Core
author: guardrex
description: Dowiedz Blazorsię więcej o funkcjach obsługi zdarzeń, w tym typach argumentów zdarzeń, wywołaniach zwrotnych zdarzeń i zarządzaniu domyślnymi zdarzeniami przeglądarki.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511369"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="5faa9-103">ASP.NET Obsługa zdarzeń Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5faa9-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="5faa9-104">Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5faa9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5faa9-105">Komponenty maszynki do golenia zapewniają funkcje obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="5faa9-105">Razor components provide event handling features.</span></span> <span data-ttu-id="5faa9-106">Dla atrybutu elementu [`@on{EVENT}`](xref:mvc/views/razor#onevent) HTML o `@onclick`nazwie (na przykład) z wartością typu delegate, składnik Razor traktuje wartość atrybutu jako program obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="5faa9-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="5faa9-107">Następujący kod wywołuje `UpdateHeading` metodę, gdy przycisk jest wybrany w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="5faa9-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="5faa9-108">Następujący kod wywołuje `CheckChanged` metodę, gdy pole wyboru zostanie zmienione w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="5faa9-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="5faa9-109">Programy obsługi zdarzeń mogą być również asynchroniczne i zwracać <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="5faa9-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="5faa9-110">Nie ma potrzeby ręcznego wywoływania [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="5faa9-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="5faa9-111">Wyjątki są rejestrowane, gdy wystąpią.</span><span class="sxs-lookup"><span data-stu-id="5faa9-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="5faa9-112">W poniższym `UpdateHeading` przykładzie jest wywoływana asynchronicznie, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="5faa9-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="5faa9-113">Typy argumentów zdarzeń</span><span class="sxs-lookup"><span data-stu-id="5faa9-113">Event argument types</span></span>

<span data-ttu-id="5faa9-114">W przypadku niektórych zdarzeń typy argumentów zdarzeń są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="5faa9-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="5faa9-115">Określenie typu zdarzenia w wywołaniu metody jest konieczne tylko wtedy, gdy typ zdarzenia jest używany w metodzie.</span><span class="sxs-lookup"><span data-stu-id="5faa9-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="5faa9-116">Obsługiwane `EventArgs` są pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="5faa9-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="5faa9-117">Wydarzenie</span><span class="sxs-lookup"><span data-stu-id="5faa9-117">Event</span></span>            | <span data-ttu-id="5faa9-118">Klasa</span><span class="sxs-lookup"><span data-stu-id="5faa9-118">Class</span></span>                | <span data-ttu-id="5faa9-119">Wydarzenia i notatki DOM</span><span class="sxs-lookup"><span data-stu-id="5faa9-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="5faa9-120">Schowek</span><span class="sxs-lookup"><span data-stu-id="5faa9-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="5faa9-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="5faa9-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="5faa9-122">Przeciągnij</span><span class="sxs-lookup"><span data-stu-id="5faa9-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="5faa9-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="5faa9-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="5faa9-124">`DataTransfer`i `DataTransferItem` przytrzymaj przeciągnięte dane elementu.</span><span class="sxs-lookup"><span data-stu-id="5faa9-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="5faa9-125">Błąd</span><span class="sxs-lookup"><span data-stu-id="5faa9-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="5faa9-126">Wydarzenie</span><span class="sxs-lookup"><span data-stu-id="5faa9-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="5faa9-127">*Ogólne*</span><span class="sxs-lookup"><span data-stu-id="5faa9-127">*General*</span></span><br><span data-ttu-id="5faa9-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="5faa9-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="5faa9-129">*Schowek*</span><span class="sxs-lookup"><span data-stu-id="5faa9-129">*Clipboard*</span></span><br><span data-ttu-id="5faa9-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="5faa9-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="5faa9-131">*Dane wejściowe*</span><span class="sxs-lookup"><span data-stu-id="5faa9-131">*Input*</span></span><br><span data-ttu-id="5faa9-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="5faa9-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="5faa9-133">*Multimedia*</span><span class="sxs-lookup"><span data-stu-id="5faa9-133">*Media*</span></span><br><span data-ttu-id="5faa9-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="5faa9-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="5faa9-135">Ostrości</span><span class="sxs-lookup"><span data-stu-id="5faa9-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="5faa9-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="5faa9-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="5faa9-137">Nie obejmuje obsługi `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="5faa9-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="5faa9-138">Dane wejściowe</span><span class="sxs-lookup"><span data-stu-id="5faa9-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="5faa9-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="5faa9-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="5faa9-140">Klawiatura</span><span class="sxs-lookup"><span data-stu-id="5faa9-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="5faa9-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="5faa9-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="5faa9-142">Mysz</span><span class="sxs-lookup"><span data-stu-id="5faa9-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="5faa9-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="5faa9-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="5faa9-144">Wskaźnik myszy</span><span class="sxs-lookup"><span data-stu-id="5faa9-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="5faa9-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="5faa9-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="5faa9-146">Kółka myszy</span><span class="sxs-lookup"><span data-stu-id="5faa9-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="5faa9-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="5faa9-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="5faa9-148">Postęp</span><span class="sxs-lookup"><span data-stu-id="5faa9-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="5faa9-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="5faa9-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="5faa9-150">Dotyk</span><span class="sxs-lookup"><span data-stu-id="5faa9-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="5faa9-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="5faa9-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="5faa9-152">`TouchPoint`oznacza pojedynczy punkt kontaktowy na urządzeniu dotykowym.</span><span class="sxs-lookup"><span data-stu-id="5faa9-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="5faa9-153">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="5faa9-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="5faa9-154">[EventArgs klasy w ASP.NET Core źródła odniesienia (dotnet/aspnetcore release/3.1 oddział)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="5faa9-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="5faa9-155">[Dokumenty sieci web MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Zawiera informacje o tym, które elementy HTML obsługują każde zdarzenie DOM.</span><span class="sxs-lookup"><span data-stu-id="5faa9-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="5faa9-156">Wyrażenia lambda</span><span class="sxs-lookup"><span data-stu-id="5faa9-156">Lambda expressions</span></span>

<span data-ttu-id="5faa9-157">[Wyrażenia Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) mogą być również używane:</span><span class="sxs-lookup"><span data-stu-id="5faa9-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="5faa9-158">Często jest wygodne, aby zamknąć dodatkowe wartości, takie jak podczas iteracji nad zestawem elementów.</span><span class="sxs-lookup"><span data-stu-id="5faa9-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="5faa9-159">Poniższy przykład tworzy trzy przyciski, z których każdy wywołuje `UpdateHeading` przekazywanie argumentu zdarzenia (`MouseEventArgs`) i jego numer przycisku (`buttonNumber`) po wybraniu w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="5faa9-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="5faa9-160">**Nie** należy używać zmiennej pętli (`i`) w `for` pętli bezpośrednio w wyrażeniu lambda.</span><span class="sxs-lookup"><span data-stu-id="5faa9-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="5faa9-161">W przeciwnym razie ta sama zmienna jest `i`używana przez wszystkie wyrażenia lambda, powodując wartość 's być taka sama we wszystkich lambdas.</span><span class="sxs-lookup"><span data-stu-id="5faa9-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="5faa9-162">Zawsze przechwytuj jego wartość`buttonNumber` w zmiennej lokalnej (w poprzednim przykładzie), a następnie użyj jej.</span><span class="sxs-lookup"><span data-stu-id="5faa9-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="5faa9-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="5faa9-163">EventCallback</span></span>

<span data-ttu-id="5faa9-164">Typowym scenariuszem z zagnieżdżonych składników jest chęć uruchomienia&mdash;metody składnika `onclick` nadrzędnego, gdy wystąpi zdarzenie składnika podrzędnego na przykład, gdy zdarzenie występuje w podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="5faa9-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="5faa9-165">Aby uwidaczniać `EventCallback`zdarzenia między składnikami, należy użyć pliku .</span><span class="sxs-lookup"><span data-stu-id="5faa9-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="5faa9-166">Składnik nadrzędny może przypisać metodę wywołania zwrotnego do składnika podrzędnego `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="5faa9-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="5faa9-167">W `ChildComponent` przykładowej aplikacji *(Components/ChildComponent.brzytwa)* pokazuje, `onclick` jak program obsługi `EventCallback` przycisku jest skonfigurowany do odbierania pełnomocnika z przykładu `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="5faa9-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="5faa9-168">Wpisuje `EventCallback` się `MouseEventArgs`z , co `onclick` jest odpowiednie dla zdarzenia z urządzenia peryferyjnego:</span><span class="sxs-lookup"><span data-stu-id="5faa9-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="5faa9-169">Ustawia `ParentComponent` dziecko `EventCallback<T>` (`OnClickCallback`) do `ShowMessage` jego metody.</span><span class="sxs-lookup"><span data-stu-id="5faa9-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="5faa9-170">*Strony/ParentComponent.brzytwa:*</span><span class="sxs-lookup"><span data-stu-id="5faa9-170">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="5faa9-171">Po wybraniu przycisku `ChildComponent`w :</span><span class="sxs-lookup"><span data-stu-id="5faa9-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="5faa9-172">Metoda `ParentComponent`'s `ShowMessage` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="5faa9-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="5faa9-173">`_messageText`jest aktualizowana i `ParentComponent`wyświetlana w pliku .</span><span class="sxs-lookup"><span data-stu-id="5faa9-173">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="5faa9-174">Wywołanie [StateHasChanged](xref:blazor/lifecycle#state-changes) nie jest wymagane w metodzie wywołania zwrotnego (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="5faa9-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="5faa9-175">`StateHasChanged`jest wywoływana automatycznie do `ParentComponent`rerender , podobnie jak zdarzenia podrzędne wyzwalania składnika rerendering w programach obsługi zdarzeń, które są wykonywane w obrębie elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="5faa9-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="5faa9-176">`EventCallback`delegatów `EventCallback<T>` asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="5faa9-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="5faa9-177">`EventCallback<T>`jest silnie wpisywany i wymaga określonego typu argumentu.</span><span class="sxs-lookup"><span data-stu-id="5faa9-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="5faa9-178">`EventCallback`jest słabo wpisywany i pozwala na dowolny typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="5faa9-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="5faa9-179">Wywołać `EventCallback` lub `EventCallback<T>` `InvokeAsync` z i <xref:System.Threading.Tasks.Task>czekać na:</span><span class="sxs-lookup"><span data-stu-id="5faa9-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="5faa9-180">Służy `EventCallback` `EventCallback<T>` i do obsługi zdarzeń i parametrów składnika wiązania.</span><span class="sxs-lookup"><span data-stu-id="5faa9-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="5faa9-181">Preferuj mocno `EventCallback<T>` `EventCallback`wpisane nad .</span><span class="sxs-lookup"><span data-stu-id="5faa9-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="5faa9-182">`EventCallback<T>`zapewnia lepsze informacje zwrotne o błędach dla użytkowników składnika.</span><span class="sxs-lookup"><span data-stu-id="5faa9-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="5faa9-183">Podobnie jak inne programy obsługi zdarzeń interfejsu użytkownika, określenie parametru zdarzenia jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="5faa9-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="5faa9-184">Użyj, `EventCallback` gdy nie ma żadnej wartości przekazywane do wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="5faa9-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="5faa9-185">Zapobiegaj działaniom domyślnym</span><span class="sxs-lookup"><span data-stu-id="5faa9-185">Prevent default actions</span></span>

<span data-ttu-id="5faa9-186">Użyj [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atrybutu dyrektywy, aby zapobiec akcji domyślnej dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="5faa9-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="5faa9-187">Gdy na urządzeniu wejściowym zaznaczono klucz, a fokus elementu znajduje się na polu tekstowym, przeglądarka zwykle wyświetla znak klucza w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="5faa9-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="5faa9-188">W poniższym przykładzie domyślne zachowanie jest `@onkeypress:preventDefault` zapobiegane przez określenie atrybutu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="5faa9-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="5faa9-189">Licznik zwiększa się, a **+** klucz nie jest `<input>` przechwytywany do wartości elementu:</span><span class="sxs-lookup"><span data-stu-id="5faa9-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="5faa9-190">Określenie atrybutu `@on{EVENT}:preventDefault` bez wartości jest `@on{EVENT}:preventDefault="true"`równoważne .</span><span class="sxs-lookup"><span data-stu-id="5faa9-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="5faa9-191">Wartość atrybutu może być również wyrażeniem.</span><span class="sxs-lookup"><span data-stu-id="5faa9-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="5faa9-192">W poniższym `_shouldPreventDefault` przykładzie `bool` jest pole `true` ustawione `false`na jeden lub:</span><span class="sxs-lookup"><span data-stu-id="5faa9-192">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="5faa9-193">Program obsługi zdarzeń nie jest wymagany, aby zapobiec akcji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="5faa9-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="5faa9-194">Program obsługi zdarzeń i zapobiec domyślne scenariusze akcji mogą być używane niezależnie.</span><span class="sxs-lookup"><span data-stu-id="5faa9-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="5faa9-195">Zatrzymaj propagację zdarzeń</span><span class="sxs-lookup"><span data-stu-id="5faa9-195">Stop event propagation</span></span>

<span data-ttu-id="5faa9-196">Użyj [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atrybutu dyrektywy, aby zatrzymać propagację zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="5faa9-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="5faa9-197">W poniższym przykładzie zaznaczenie pola wyboru zapobiega propagacji zdarzeń kliknięć z drugiego dziecka `<div>` do nadrzędnego: `<div>`</span><span class="sxs-lookup"><span data-stu-id="5faa9-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
