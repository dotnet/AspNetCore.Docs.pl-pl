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
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Obsługa zdarzeń Core Blazor

Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)

Komponenty maszynki do golenia zapewniają funkcje obsługi zdarzeń. Dla atrybutu elementu [`@on{EVENT}`](xref:mvc/views/razor#onevent) HTML o `@onclick`nazwie (na przykład) z wartością typu delegate, składnik Razor traktuje wartość atrybutu jako program obsługi zdarzeń.

Następujący kod wywołuje `UpdateHeading` metodę, gdy przycisk jest wybrany w interfejsie użytkownika:

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

Następujący kod wywołuje `CheckChanged` metodę, gdy pole wyboru zostanie zmienione w interfejsie użytkownika:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Programy obsługi zdarzeń mogą być również asynchroniczne i zwracać <xref:System.Threading.Tasks.Task>. Nie ma potrzeby ręcznego wywoływania [StateHasChanged](xref:blazor/lifecycle#state-changes). Wyjątki są rejestrowane, gdy wystąpią.

W poniższym `UpdateHeading` przykładzie jest wywoływana asynchronicznie, gdy przycisk jest zaznaczony:

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

## <a name="event-argument-types"></a>Typy argumentów zdarzeń

W przypadku niektórych zdarzeń typy argumentów zdarzeń są dozwolone. Określenie typu zdarzenia w wywołaniu metody jest konieczne tylko wtedy, gdy typ zdarzenia jest używany w metodzie.

Obsługiwane `EventArgs` są pokazane w poniższej tabeli.

| Wydarzenie            | Klasa                | Wydarzenia i notatki DOM |
| ---------------- | -------------------- | -------------------- |
| Schowek        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Przeciągnij             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer`i `DataTransferItem` przytrzymaj przeciągnięte dane elementu. |
| Błąd            | `ErrorEventArgs`     | `onerror` |
| Wydarzenie            | `EventArgs`          | *Ogólne*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Schowek*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Dane wejściowe*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Multimedia*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Ostrości            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Nie obejmuje obsługi `relatedTarget`. |
| Dane wejściowe            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Klawiatura         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Mysz            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Wskaźnik myszy    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Kółka myszy      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Postęp         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Dotyk            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint`oznacza pojedynczy punkt kontaktowy na urządzeniu dotykowym. |

Więcej informacji zawierają następujące zasoby:

* [EventArgs klasy w ASP.NET Core źródła odniesienia (dotnet/aspnetcore release/3.1 oddział)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [Dokumenty sieci web MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Zawiera informacje o tym, które elementy HTML obsługują każde zdarzenie DOM.

## <a name="lambda-expressions"></a>Wyrażenia lambda

[Wyrażenia Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) mogą być również używane:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Często jest wygodne, aby zamknąć dodatkowe wartości, takie jak podczas iteracji nad zestawem elementów. Poniższy przykład tworzy trzy przyciski, z których każdy wywołuje `UpdateHeading` przekazywanie argumentu zdarzenia (`MouseEventArgs`) i jego numer przycisku (`buttonNumber`) po wybraniu w interfejsie użytkownika:

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
> **Nie** należy używać zmiennej pętli (`i`) w `for` pętli bezpośrednio w wyrażeniu lambda. W przeciwnym razie ta sama zmienna jest `i`używana przez wszystkie wyrażenia lambda, powodując wartość 's być taka sama we wszystkich lambdas. Zawsze przechwytuj jego wartość`buttonNumber` w zmiennej lokalnej (w poprzednim przykładzie), a następnie użyj jej.

## <a name="eventcallback"></a>EventCallback

Typowym scenariuszem z zagnieżdżonych składników jest chęć uruchomienia&mdash;metody składnika `onclick` nadrzędnego, gdy wystąpi zdarzenie składnika podrzędnego na przykład, gdy zdarzenie występuje w podrzędnym. Aby uwidaczniać `EventCallback`zdarzenia między składnikami, należy użyć pliku . Składnik nadrzędny może przypisać metodę wywołania zwrotnego do składnika podrzędnego `EventCallback`.

W `ChildComponent` przykładowej aplikacji *(Components/ChildComponent.brzytwa)* pokazuje, `onclick` jak program obsługi `EventCallback` przycisku jest skonfigurowany do odbierania pełnomocnika z przykładu `ParentComponent`. Wpisuje `EventCallback` się `MouseEventArgs`z , co `onclick` jest odpowiednie dla zdarzenia z urządzenia peryferyjnego:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Ustawia `ParentComponent` dziecko `EventCallback<T>` (`OnClickCallback`) do `ShowMessage` jego metody.

*Strony/ParentComponent.brzytwa:*

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

Po wybraniu przycisku `ChildComponent`w :

* Metoda `ParentComponent`'s `ShowMessage` jest wywoływana. `_messageText`jest aktualizowana i `ParentComponent`wyświetlana w pliku .
* Wywołanie [StateHasChanged](xref:blazor/lifecycle#state-changes) nie jest wymagane w metodzie wywołania zwrotnego (`ShowMessage`). `StateHasChanged`jest wywoływana automatycznie do `ParentComponent`rerender , podobnie jak zdarzenia podrzędne wyzwalania składnika rerendering w programach obsługi zdarzeń, które są wykonywane w obrębie elementu podrzędnego.

`EventCallback`delegatów `EventCallback<T>` asynchronicznych. `EventCallback<T>`jest silnie wpisywany i wymaga określonego typu argumentu. `EventCallback`jest słabo wpisywany i pozwala na dowolny typ argumentu.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Wywołać `EventCallback` lub `EventCallback<T>` `InvokeAsync` z i <xref:System.Threading.Tasks.Task>czekać na:

```csharp
await callback.InvokeAsync(arg);
```

Służy `EventCallback` `EventCallback<T>` i do obsługi zdarzeń i parametrów składnika wiązania.

Preferuj mocno `EventCallback<T>` `EventCallback`wpisane nad . `EventCallback<T>`zapewnia lepsze informacje zwrotne o błędach dla użytkowników składnika. Podobnie jak inne programy obsługi zdarzeń interfejsu użytkownika, określenie parametru zdarzenia jest opcjonalne. Użyj, `EventCallback` gdy nie ma żadnej wartości przekazywane do wywołania zwrotnego.

## <a name="prevent-default-actions"></a>Zapobiegaj działaniom domyślnym

Użyj [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atrybutu dyrektywy, aby zapobiec akcji domyślnej dla zdarzenia.

Gdy na urządzeniu wejściowym zaznaczono klucz, a fokus elementu znajduje się na polu tekstowym, przeglądarka zwykle wyświetla znak klucza w polu tekstowym. W poniższym przykładzie domyślne zachowanie jest `@onkeypress:preventDefault` zapobiegane przez określenie atrybutu dyrektywy. Licznik zwiększa się, a **+** klucz nie jest `<input>` przechwytywany do wartości elementu:

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

Określenie atrybutu `@on{EVENT}:preventDefault` bez wartości jest `@on{EVENT}:preventDefault="true"`równoważne .

Wartość atrybutu może być również wyrażeniem. W poniższym `_shouldPreventDefault` przykładzie `bool` jest pole `true` ustawione `false`na jeden lub:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Program obsługi zdarzeń nie jest wymagany, aby zapobiec akcji domyślnej. Program obsługi zdarzeń i zapobiec domyślne scenariusze akcji mogą być używane niezależnie.

## <a name="stop-event-propagation"></a>Zatrzymaj propagację zdarzeń

Użyj [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atrybutu dyrektywy, aby zatrzymać propagację zdarzeń.

W poniższym przykładzie zaznaczenie pola wyboru zapobiega propagacji zdarzeń kliknięć z drugiego dziecka `<div>` do nadrzędnego: `<div>`

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
