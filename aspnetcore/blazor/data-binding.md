---
title: ASP.NET podstawowe Blazor powiązanie danych
author: guardrex
description: Dowiedz się więcej o funkcjach Blazor wiązania danych dla składników i elementów DOM w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320950"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="2fb23-103">ASP.NET podstawowe Blazor powiązanie danych</span><span class="sxs-lookup"><span data-stu-id="2fb23-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="2fb23-104">Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2fb23-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2fb23-105">Składniki maszynki do golenia zapewniają funkcje [`@bind`](xref:mvc/views/razor#bind) wiązania danych za pośrednictwem atrybutu elementu HTML o nazwie z wartością wyrażenia pole, właściwość lub razor.</span><span class="sxs-lookup"><span data-stu-id="2fb23-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="2fb23-106">Poniższy przykład wiąże `CurrentValue` właściwość z wartością pola tekstowego:</span><span class="sxs-lookup"><span data-stu-id="2fb23-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="2fb23-107">Gdy pole tekstowe traci fokus, wartość właściwości jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="2fb23-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="2fb23-108">Pole tekstowe jest aktualizowane w interfejsie użytkownika tylko wtedy, gdy składnik jest renderowany, a nie w odpowiedzi na zmianę wartości właściwości.</span><span class="sxs-lookup"><span data-stu-id="2fb23-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="2fb23-109">Ponieważ składniki renderowania się po wykonaniu kodu obsługi zdarzeń, aktualizacje właściwości są *zwykle* odzwierciedlane w interfejsie użytkownika natychmiast po wyzwoleniu programu obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="2fb23-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="2fb23-110">Korzystanie `@bind` z `CurrentValue` właściwości`<input @bind="CurrentValue" />`( ) jest zasadniczo równoważne z następującymi:</span><span class="sxs-lookup"><span data-stu-id="2fb23-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="2fb23-111">Gdy składnik jest renderowany, `value` element wejściowy `CurrentValue` pochodzi z właściwości.</span><span class="sxs-lookup"><span data-stu-id="2fb23-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="2fb23-112">Gdy użytkownik wpisuje w polu tekstowym `onchange` i zmienia fokus elementu, zdarzenie jest uruchamiane i `CurrentValue` właściwość jest ustawiona na zmienioną wartość.</span><span class="sxs-lookup"><span data-stu-id="2fb23-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="2fb23-113">W rzeczywistości generowanie kodu jest `@bind` bardziej złożone, ponieważ obsługuje przypadki, w których są wykonywane konwersje typu.</span><span class="sxs-lookup"><span data-stu-id="2fb23-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="2fb23-114">Zasadniczo `@bind` kojarzy bieżącą wartość wyrażenia `value` z atrybutem i obsługuje zmiany przy użyciu zarejestrowanego programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="2fb23-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="2fb23-115">Powiąż właściwość lub pole `@bind:event` z innymi `event` zdarzeniami, dołączając również atrybut z parametrem.</span><span class="sxs-lookup"><span data-stu-id="2fb23-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="2fb23-116">Poniższy przykład wiąże `CurrentValue` właściwość `oninput` ze zdarzeniem:</span><span class="sxs-lookup"><span data-stu-id="2fb23-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="2fb23-117">W `onchange`przeciwieństwie do , który uruchamia `oninput` się, gdy element traci fokus, uruchamia się, gdy zmienia się wartość pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="2fb23-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="2fb23-118">Użyj `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` ze składnią do wiązania `value`atrybutów elementów innych niż .</span><span class="sxs-lookup"><span data-stu-id="2fb23-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="2fb23-119">W poniższym przykładzie styl akapitu `_paragraphStyle` jest aktualizowany po zmianie wartości:</span><span class="sxs-lookup"><span data-stu-id="2fb23-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

<span data-ttu-id="2fb23-120">Rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="2fb23-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="2fb23-121">Na przykład `@bind` jest prawidłowy i `@Bind` jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="2fb23-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="2fb23-122">Wartości niedajne</span><span class="sxs-lookup"><span data-stu-id="2fb23-122">Unparsable values</span></span>

<span data-ttu-id="2fb23-123">Gdy użytkownik udostępnia nieparadowalną wartość elementu połączenia danych, wartość nieparasable jest automatycznie przywracana do poprzedniej wartości po wyzwoleniu zdarzenia powiązania.</span><span class="sxs-lookup"><span data-stu-id="2fb23-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="2fb23-124">Poniżej przedstawiono przykładowy scenariusz:</span><span class="sxs-lookup"><span data-stu-id="2fb23-124">Consider the following scenario:</span></span>

* <span data-ttu-id="2fb23-125">Element `<input>` jest powiązany `int` z typem `123`o wartości początkowej:</span><span class="sxs-lookup"><span data-stu-id="2fb23-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="2fb23-126">Użytkownik aktualizuje wartość elementu `123.45` na stronie i zmienia fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="2fb23-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="2fb23-127">W poprzednim scenariuszu wartość elementu jest przywracana `123`do .</span><span class="sxs-lookup"><span data-stu-id="2fb23-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="2fb23-128">Gdy wartość `123.45` zostanie odrzucona na rzecz `123`oryginalnej wartości , użytkownik rozumie, że ich wartość nie została zaakceptowana.</span><span class="sxs-lookup"><span data-stu-id="2fb23-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="2fb23-129">Domyślnie powiązanie ma zastosowanie do `onchange` zdarzenia`@bind="{PROPERTY OR FIELD}"`elementu ( ).</span><span class="sxs-lookup"><span data-stu-id="2fb23-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="2fb23-130">Służy `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` do wyzwalania powiązania dla innego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="2fb23-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="2fb23-131">W `oninput` przypadku`@bind:event="oninput"`zdarzenia ( ), odwrócenie występuje po każdym naciśnięciu klawisza, który wprowadza wartość niedościsobie.</span><span class="sxs-lookup"><span data-stu-id="2fb23-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="2fb23-132">Podczas kierowania `oninput` zdarzenia z `int`typem powiązanym użytkownik nie może `.` wpisywać znaku.</span><span class="sxs-lookup"><span data-stu-id="2fb23-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="2fb23-133">Znak `.` jest natychmiast usuwany, więc użytkownik otrzymuje natychmiastową informację, że dozwolone są tylko liczby pełne.</span><span class="sxs-lookup"><span data-stu-id="2fb23-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="2fb23-134">Istnieją scenariusze, w których przywracanie wartości w zdarzeniu `oninput` nie jest idealne, na przykład gdy `<input>` użytkownik powinien mieć możliwość wyczyszczenia wartości niepardziellnej.</span><span class="sxs-lookup"><span data-stu-id="2fb23-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="2fb23-135">Alternatywy obejmują:</span><span class="sxs-lookup"><span data-stu-id="2fb23-135">Alternatives include:</span></span>

* <span data-ttu-id="2fb23-136">Nie używaj `oninput` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="2fb23-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="2fb23-137">Użyj zdarzenia `onchange` domyślnego `@bind="{PROPERTY OR FIELD}"`(tylko określić ), gdzie nieprawidłowa wartość nie jest przywracana, dopóki element traci fokus.</span><span class="sxs-lookup"><span data-stu-id="2fb23-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="2fb23-138">Powiązanie z typem nullable, takich jak `int?` lub `string`, i podać niestandardową logikę do obsługi nieprawidłowych wpisów.</span><span class="sxs-lookup"><span data-stu-id="2fb23-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="2fb23-139">Użyj [składnika sprawdzania poprawności formularza,](xref:blazor/forms-validation)takiego jak `InputNumber` lub `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="2fb23-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="2fb23-140">Składniki sprawdzania poprawności formularza mają wbudowaną obsługę zarządzania nieprawidłowymi danymi wejściowymi.</span><span class="sxs-lookup"><span data-stu-id="2fb23-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="2fb23-141">Składniki sprawdzania poprawności formularza:</span><span class="sxs-lookup"><span data-stu-id="2fb23-141">Form validation components:</span></span>
  * <span data-ttu-id="2fb23-142">Zezwalaj użytkownikowi na podanie nieprawidłowych danych wejściowych `EditContext`i odbierania błędów sprawdzania poprawności na skojarzonym programie .</span><span class="sxs-lookup"><span data-stu-id="2fb23-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="2fb23-143">Wyświetlanie błędów sprawdzania poprawności w interfejsie użytkownika bez zakłócania wprowadzania przez użytkownika dodatkowych danych webform.</span><span class="sxs-lookup"><span data-stu-id="2fb23-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="2fb23-144">Formatowanie ciągów</span><span class="sxs-lookup"><span data-stu-id="2fb23-144">Format strings</span></span>

<span data-ttu-id="2fb23-145">Powiązanie danych <xref:System.DateTime> działa z [`@bind:format`](xref:mvc/views/razor#bind)ciągami formatu przy użyciu programu .</span><span class="sxs-lookup"><span data-stu-id="2fb23-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="2fb23-146">Inne wyrażenia formatu, takie jak formaty walut lub liczb, nie są obecnie dostępne.</span><span class="sxs-lookup"><span data-stu-id="2fb23-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="2fb23-147">W poprzednim kodzie `<input>` typ pola elementu`type`( ) `text`domyślnie ma wartość .</span><span class="sxs-lookup"><span data-stu-id="2fb23-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="2fb23-148">`@bind:format`jest obsługiwana w celu powiązania następujących typów .NET:</span><span class="sxs-lookup"><span data-stu-id="2fb23-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="2fb23-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="2fb23-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="2fb23-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="2fb23-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="2fb23-151">Atrybut `@bind:format` określa format daty, który `value` ma `<input>` być stosowany do elementu.</span><span class="sxs-lookup"><span data-stu-id="2fb23-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="2fb23-152">Format jest również używany do analizowania `onchange` wartości w przypadku wystąpienia zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="2fb23-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="2fb23-153">Określanie formatu `date` dla typu pola nie Blazor jest zalecane, ponieważ ma wbudowaną obsługę formatowania dat.</span><span class="sxs-lookup"><span data-stu-id="2fb23-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="2fb23-154">Pomimo zalecenia, należy używać `yyyy-MM-dd` tylko format daty do wiązania do poprawnego działania, jeśli format jest dostarczany z typem `date` pola:</span><span class="sxs-lookup"><span data-stu-id="2fb23-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="2fb23-155">Powiązanie nadrzędny-podrzędny z parametrami komponentu</span><span class="sxs-lookup"><span data-stu-id="2fb23-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="2fb23-156">Powiązanie rozpoznaje parametry `@bind-{PROPERTY}` składnika, gdzie można powiązać wartość właściwości ze składnika nadrzędnego w dół do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="2fb23-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="2fb23-157">Powiązanie z elementem podrzędnym do rodzica jest objęte [Child-to-parent powiązania z sieciowej](#child-to-parent-binding-with-chained-bind) sekcji powiązania.</span><span class="sxs-lookup"><span data-stu-id="2fb23-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="2fb23-158">Następujący składnik podrzędny`ChildComponent`( `Year` ) `YearChanged` ma parametr składnika i wywołanie zwrotne:</span><span class="sxs-lookup"><span data-stu-id="2fb23-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="2fb23-159">`EventCallback<T>`wyjaśniono w <xref:blazor/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="2fb23-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="2fb23-160">Następujący składnik nadrzędny używa:</span><span class="sxs-lookup"><span data-stu-id="2fb23-160">The following parent component uses:</span></span>

* <span data-ttu-id="2fb23-161">`ChildComponent`i wiąże `ParentYear` parametr z elementem `Year` nadrzędnym z parametrem w komponencie podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="2fb23-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="2fb23-162">Zdarzenie `onclick` jest używane do `ChangeTheYear` wyzwalania metody.</span><span class="sxs-lookup"><span data-stu-id="2fb23-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="2fb23-163">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="2fb23-163">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="2fb23-164">Ładowanie `ParentComponent` powoduje następujące znaczniki:</span><span class="sxs-lookup"><span data-stu-id="2fb23-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="2fb23-165">Jeśli wartość właściwości `ParentYear` zostanie zmieniona przez wybranie `ParentComponent`przycisku w , `Year` właściwość `ChildComponent` jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="2fb23-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="2fb23-166">Nowa wartość `Year` jest renderowana w interfejsie `ParentComponent` użytkownika, gdy jest rerendered:</span><span class="sxs-lookup"><span data-stu-id="2fb23-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="2fb23-167">Parametr `Year` jest wiązany, ponieważ `YearChanged` ma zdarzenie towarzyszące, które pasuje do typu parametru. `Year`</span><span class="sxs-lookup"><span data-stu-id="2fb23-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="2fb23-168">Zgodnie z `<ChildComponent @bind-Year="ParentYear" />` konwencją, jest zasadniczo równoważne piśmie:</span><span class="sxs-lookup"><span data-stu-id="2fb23-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="2fb23-169">Ogólnie rzecz biorąc właściwość może być powiązana `@bind-{PROPRETY}:event` z odpowiednim programem obsługi zdarzeń, dołączając atrybut.</span><span class="sxs-lookup"><span data-stu-id="2fb23-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="2fb23-170">Na przykład właściwość `MyProp` może `MyEventHandler` być powiązana przy użyciu następujących dwóch atrybutów:</span><span class="sxs-lookup"><span data-stu-id="2fb23-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="2fb23-171">Powiązanie podrzędne z elementem nadrzędnym z powiązaniem łańcuchowym</span><span class="sxs-lookup"><span data-stu-id="2fb23-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="2fb23-172">Typowym scenariuszem jest łączenie parametru powiązanego z danymi do elementu strony w danych wyjściowych składnika.</span><span class="sxs-lookup"><span data-stu-id="2fb23-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="2fb23-173">Ten scenariusz jest nazywany *powiązanie łańcuchowe,* ponieważ wiele poziomów wiązania występują jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="2fb23-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="2fb23-174">Powiązanie łańcuchowe nie może być `@bind` zaimplementowane ze składnią w elemencie strony.</span><span class="sxs-lookup"><span data-stu-id="2fb23-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="2fb23-175">Program obsługi zdarzeń i wartość musi być określony oddzielnie.</span><span class="sxs-lookup"><span data-stu-id="2fb23-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="2fb23-176">Składnik nadrzędny może jednak `@bind` używać składni z parametrem składnika.</span><span class="sxs-lookup"><span data-stu-id="2fb23-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="2fb23-177">Następujący `PasswordField` składnik (*PasswordField.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="2fb23-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="2fb23-178">Ustawia `<input>` wartość elementu na `Password` właściwość.</span><span class="sxs-lookup"><span data-stu-id="2fb23-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="2fb23-179">Udostępnia zmiany `Password` właściwości do składnika nadrzędnego z [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="2fb23-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="2fb23-180">Używa `onclick` zdarzenia jest używany do `ToggleShowPassword` wyzwalania metody.</span><span class="sxs-lookup"><span data-stu-id="2fb23-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="2fb23-181">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="2fb23-181">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="2fb23-182">Składnik `PasswordField` jest używany w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="2fb23-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="2fb23-183">Aby wykonać błędy sprawdzania lub zalewkowania hasła w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2fb23-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="2fb23-184">Utwórz pole `Password` zapasowe dla (`_password` w poniższym przykładzie kodu).</span><span class="sxs-lookup"><span data-stu-id="2fb23-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="2fb23-185">Wykonaj błędy kontroli lub zalewki w ustawiacza. `Password`</span><span class="sxs-lookup"><span data-stu-id="2fb23-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="2fb23-186">Poniższy przykład zapewnia natychmiastową informację zwrotną dla użytkownika, jeśli spacja jest używana w wartości hasła:</span><span class="sxs-lookup"><span data-stu-id="2fb23-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="2fb23-187">Przycisków</span><span class="sxs-lookup"><span data-stu-id="2fb23-187">Radio buttons</span></span>

<span data-ttu-id="2fb23-188">Aby uzyskać informacje na temat powiązania <xref:blazor/forms-validation#work-with-radio-buttons>z przyciskami radiowymi w formularzu, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2fb23-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
