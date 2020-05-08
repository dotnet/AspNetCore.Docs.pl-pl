---
title: ASP.NET Core Blazor powiązania danych
author: guardrex
description: Dowiedz się więcej o funkcjach powiązań danych dla składników Blazor i elementów modelu dom w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: b4951c5eb712b15db3a7c1ccd57ae01c530a23ef
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967171"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="2a59d-103">ASP.NET Core Blazor powiązania danych</span><span class="sxs-lookup"><span data-stu-id="2a59d-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="2a59d-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2a59d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="2a59d-105">składniki zapewniają funkcje powiązań danych za pośrednictwem atrybutu elementu HTML [`@bind`](xref:mvc/views/razor#bind) o nazwie z wartością pola, właściwości Razor lub wyrażenia.</span><span class="sxs-lookup"><span data-stu-id="2a59d-105"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="2a59d-106">Poniższy przykład wiąże `CurrentValue` właściwość z wartością pola tekstowego:</span><span class="sxs-lookup"><span data-stu-id="2a59d-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="2a59d-107">Gdy pole tekstowe utraci fokus, wartość właściwości jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="2a59d-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="2a59d-108">Pole tekstowe jest aktualizowane w interfejsie użytkownika tylko wtedy, gdy składnik jest renderowany, a nie w odpowiedzi na zmianę wartości właściwości.</span><span class="sxs-lookup"><span data-stu-id="2a59d-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="2a59d-109">Ponieważ składniki renderują się po wykonaniu kodu procedury obsługi zdarzeń, aktualizacje właściwości są *zwykle* odzwierciedlane w interfejsie użytkownika natychmiast po wyzwoleniu programu obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="2a59d-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="2a59d-110">Używanie `@bind` z `CurrentValue` właściwością (`<input @bind="CurrentValue" />`) jest zasadniczo równoważne z następującymi:</span><span class="sxs-lookup"><span data-stu-id="2a59d-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="2a59d-111">Gdy składnik jest renderowany, `value` element wejściowy pochodzi z `CurrentValue` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2a59d-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="2a59d-112">Gdy użytkownik wpisze w polu tekstowym i zmieni fokus elementu, `onchange` zdarzenie jest wywoływane i `CurrentValue` właściwość jest ustawiana na wartość zmieniona.</span><span class="sxs-lookup"><span data-stu-id="2a59d-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="2a59d-113">W rzeczywistości generowanie kodu jest bardziej skomplikowane, ponieważ `@bind` obsługuje przypadki, w których są wykonywane konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="2a59d-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="2a59d-114">W zasadzie `@bind` kojarzy bieżącą wartość wyrażenia z `value` atrybutem i obsługuje zmiany przy użyciu zarejestrowanej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="2a59d-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="2a59d-115">Powiąż właściwość lub pole w innych zdarzeniach, dołączając także `@bind:event` atrybut z `event` parametrem.</span><span class="sxs-lookup"><span data-stu-id="2a59d-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="2a59d-116">Poniższy przykład wiąże `CurrentValue` Właściwość `oninput` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="2a59d-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="2a59d-117">W przeciwieństwie do `onchange`, które jest wyzwalane, gdy `oninput` element utraci fokus, jest uruchamiany po zmianie wartości pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="2a59d-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="2a59d-118">Użyj `@bind-{ATTRIBUTE}` składni `@bind-{ATTRIBUTE}:event` with, aby powiązać atrybuty elementu inne `value`niż.</span><span class="sxs-lookup"><span data-stu-id="2a59d-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="2a59d-119">W poniższym przykładzie styl akapitu zostanie zaktualizowany po zmianie `paragraphStyle` wartości:</span><span class="sxs-lookup"><span data-stu-id="2a59d-119">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="2a59d-120">W powiązaniu atrybutu rozróżniana jest wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="2a59d-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="2a59d-121">Na przykład `@bind` prawidłowe i `@Bind` jest nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="2a59d-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="2a59d-122">Wartości niemożliwy do przeanalizowania</span><span class="sxs-lookup"><span data-stu-id="2a59d-122">Unparsable values</span></span>

<span data-ttu-id="2a59d-123">Gdy użytkownik dostarczy wartość niemożliwy do przeanalizowania do elementu powiązanego z danymi, wartość niemożliwy do przeanalizowania jest automatycznie przywracana do poprzedniej wartości po wyzwoleniu zdarzenia bind.</span><span class="sxs-lookup"><span data-stu-id="2a59d-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="2a59d-124">Poniżej przedstawiono przykładowy scenariusz:</span><span class="sxs-lookup"><span data-stu-id="2a59d-124">Consider the following scenario:</span></span>

* <span data-ttu-id="2a59d-125">`<input>` Element jest powiązany z `int` typem z początkową wartością `123`:</span><span class="sxs-lookup"><span data-stu-id="2a59d-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="2a59d-126">Użytkownik aktualizuje wartość elementu na `123.45` liście na stronie i zmienia fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="2a59d-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="2a59d-127">W poprzednim scenariuszu wartość elementu jest przywracana `123`.</span><span class="sxs-lookup"><span data-stu-id="2a59d-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="2a59d-128">Gdy wartość `123.45` zostanie odrzucona na korzyść oryginalnej wartości `123`, użytkownik rozumie, że ich wartość nie została zaakceptowana.</span><span class="sxs-lookup"><span data-stu-id="2a59d-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="2a59d-129">Domyślnie powiązanie dotyczy `onchange` zdarzenia elementu (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="2a59d-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="2a59d-130">Służy `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` do wyzwalania powiązań na innym zdarzeniu.</span><span class="sxs-lookup"><span data-stu-id="2a59d-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="2a59d-131">W przypadku `oninput` zdarzenia (`@bind:event="oninput"`) jego wersja następuje po naciśnięciu klawisza, które wprowadza niemożliwy do przeanalizowania wartość.</span><span class="sxs-lookup"><span data-stu-id="2a59d-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="2a59d-132">Podczas określania wartości `oninput` docelowej dla zdarzenia `int`z typem związanym użytkownik nie jest w trakcie wpisywania `.` znaku.</span><span class="sxs-lookup"><span data-stu-id="2a59d-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="2a59d-133">`.` Znak zostanie natychmiast usunięty, więc użytkownik otrzymuje natychmiastową opinię, że dozwolone są tylko liczby całkowite.</span><span class="sxs-lookup"><span data-stu-id="2a59d-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="2a59d-134">Istnieją scenariusze, w których przywrócenie wartości `oninput` zdarzenia nie jest idealne, na przykład wtedy, gdy użytkownik powinien mieć możliwość wyczyszczenia `<input>` wartości, która nie może być przewidziana.</span><span class="sxs-lookup"><span data-stu-id="2a59d-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="2a59d-135">Alternatywy obejmują:</span><span class="sxs-lookup"><span data-stu-id="2a59d-135">Alternatives include:</span></span>

* <span data-ttu-id="2a59d-136">Nie używaj `oninput` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="2a59d-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="2a59d-137">Użyj zdarzenia domyślnego `onchange` (tylko określenie `@bind="{PROPERTY OR FIELD}"`), gdzie nie jest przywracana nieprawidłowa wartość, dopóki element nie utraci fokusu.</span><span class="sxs-lookup"><span data-stu-id="2a59d-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="2a59d-138">Powiąż z typem dopuszczającym wartość `int?` null `string`, taką jak lub, i podaj logikę niestandardową do obsługi nieprawidłowych wpisów.</span><span class="sxs-lookup"><span data-stu-id="2a59d-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="2a59d-139">Użyj [składnika walidacji formularza](xref:blazor/forms-validation), takiego jak `InputNumber` lub `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="2a59d-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="2a59d-140">Składniki walidacji formularza mają wbudowaną obsługę zarządzania nieprawidłowymi danymi wejściowymi.</span><span class="sxs-lookup"><span data-stu-id="2a59d-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="2a59d-141">Składniki walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="2a59d-141">Form validation components:</span></span>
  * <span data-ttu-id="2a59d-142">Zezwalaj użytkownikowi na dostarczenie nieprawidłowych danych wejściowych i odbieranie błędów walidacji w skojarzonym `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="2a59d-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="2a59d-143">Wyświetlaj błędy walidacji w interfejsie użytkownika bez zakłócania wprowadzania dodatkowych danych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2a59d-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="2a59d-144">Ciągi formatujące</span><span class="sxs-lookup"><span data-stu-id="2a59d-144">Format strings</span></span>

<span data-ttu-id="2a59d-145">Powiązanie danych działa z <xref:System.DateTime> ciągami formatu [`@bind:format`](xref:mvc/views/razor#bind)przy użyciu.</span><span class="sxs-lookup"><span data-stu-id="2a59d-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="2a59d-146">W tej chwili nie są dostępne inne wyrażenia formatu, takie jak formaty walutowe lub liczbowe.</span><span class="sxs-lookup"><span data-stu-id="2a59d-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="2a59d-147">W poprzednim kodzie typ pola ( `<input>` `type`) elementu jest wartością domyślną `text`.</span><span class="sxs-lookup"><span data-stu-id="2a59d-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="2a59d-148">`@bind:format`jest obsługiwana w celu powiązania następujących typów .NET:</span><span class="sxs-lookup"><span data-stu-id="2a59d-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="2a59d-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="2a59d-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="2a59d-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="2a59d-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="2a59d-151">Ten `@bind:format` atrybut określa format daty, który ma zostać zastosowany `value` do `<input>` elementu.</span><span class="sxs-lookup"><span data-stu-id="2a59d-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="2a59d-152">Format jest również używany do analizowania wartości w przypadku wystąpienia `onchange` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="2a59d-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="2a59d-153">Określanie formatu dla typu `date` pola nie jest zalecane, ponieważ Blazor ma wbudowaną obsługę formatowania dat.</span><span class="sxs-lookup"><span data-stu-id="2a59d-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="2a59d-154">Pomimo zalecenia, należy używać tylko formatu `yyyy-MM-dd` daty do poprawnego działania, jeśli format jest dostarczany z typem `date` pola:</span><span class="sxs-lookup"><span data-stu-id="2a59d-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="2a59d-155">Powiązanie element nadrzędny-to-Child z parametrami składnika</span><span class="sxs-lookup"><span data-stu-id="2a59d-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="2a59d-156">Powiązanie rozpoznaje parametry składnika, gdzie `@bind-{PROPERTY}` można powiązać wartość właściwości z składnika nadrzędnego w dół ze składnikiem podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="2a59d-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="2a59d-157">Powiązanie z elementu podrzędnego z elementem nadrzędnym jest omówione w [powiązaniu podrzędnie-to-Parent z częściowym powiązaniem powiązania](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="2a59d-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="2a59d-158">Następujący składnik podrzędny (`ChildComponent`) ma parametr `Year` składnika i `YearChanged` wywołanie zwrotne:</span><span class="sxs-lookup"><span data-stu-id="2a59d-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="2a59d-159">`EventCallback<T>`wyjaśniono w <xref:blazor/event-handling#eventcallback>temacie.</span><span class="sxs-lookup"><span data-stu-id="2a59d-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="2a59d-160">Poniższy składnik nadrzędny używa:</span><span class="sxs-lookup"><span data-stu-id="2a59d-160">The following parent component uses:</span></span>

* <span data-ttu-id="2a59d-161">`ChildComponent`i wiąże `ParentYear` parametr z elementu nadrzędnego z `Year` parametrem w składniku podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="2a59d-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="2a59d-162">To `onclick` zdarzenie służy do wyzwalania `ChangeTheYear` metody.</span><span class="sxs-lookup"><span data-stu-id="2a59d-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="2a59d-163">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="2a59d-163">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="2a59d-164">Załadowanie `ParentComponent` powoduje utworzenie następującej adjustacji:</span><span class="sxs-lookup"><span data-stu-id="2a59d-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="2a59d-165">Jeśli wartość `ParentYear` właściwości zostanie zmieniona przez wybranie przycisku w `ParentComponent`, `Year` Właściwość `ChildComponent` jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="2a59d-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="2a59d-166">Nowa wartość `Year` jest renderowana w interfejsie użytkownika podczas jego `ParentComponent` renderowania:</span><span class="sxs-lookup"><span data-stu-id="2a59d-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="2a59d-167">`Year` Parametr jest możliwy do powiązania, ponieważ ma zdarzenie towarzyszące `YearChanged` pasujące do typu `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="2a59d-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="2a59d-168">Zgodnie z Konwencją, `<ChildComponent @bind-Year="ParentYear" />` jest zasadniczo równoważne zapisowi:</span><span class="sxs-lookup"><span data-stu-id="2a59d-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="2a59d-169">Ogólnie rzecz biorąc, właściwość może być powiązana z odpowiadającą jej obsługą zdarzeń `@bind-{PROPRETY}:event` przez uwzględnienie atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2a59d-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="2a59d-170">Na przykład właściwość `MyProp` może być powiązana z `MyEventHandler` użyciem następujących dwóch atrybutów:</span><span class="sxs-lookup"><span data-stu-id="2a59d-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="2a59d-171">Powiązanie elementu podrzędnego z elementem nadrzędnym z powiązaniem łańcuchowym</span><span class="sxs-lookup"><span data-stu-id="2a59d-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="2a59d-172">Typowy scenariusz polega na łańcuchu parametru powiązanego z danymi do elementu strony w danych wyjściowych składnika.</span><span class="sxs-lookup"><span data-stu-id="2a59d-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="2a59d-173">Ten scenariusz jest nazywany *powiązaniem łańcuchowym* , ponieważ wiele poziomów powiązań występuje jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="2a59d-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="2a59d-174">Nie można zaimplementować powiązania łańcuchowego przy użyciu `@bind` składni w elemencie strony.</span><span class="sxs-lookup"><span data-stu-id="2a59d-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="2a59d-175">Program obsługi zdarzeń i wartość muszą być określone osobno.</span><span class="sxs-lookup"><span data-stu-id="2a59d-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="2a59d-176">Składnik nadrzędny, jednak może używać `@bind` składni z parametrem składnika.</span><span class="sxs-lookup"><span data-stu-id="2a59d-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="2a59d-177">Następujący `PasswordField` składnik (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="2a59d-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="2a59d-178">Ustawia wartość `<input>` elementu na `Password` właściwość.</span><span class="sxs-lookup"><span data-stu-id="2a59d-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="2a59d-179">Uwidacznia zmiany `Password` właściwości w składniku nadrzędnym z [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="2a59d-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="2a59d-180">Używa `onclick` zdarzenia, aby wyzwolić `ToggleShowPassword` metodę.</span><span class="sxs-lookup"><span data-stu-id="2a59d-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="2a59d-181">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="2a59d-181">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

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
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="2a59d-182">`PasswordField` Składnik jest używany w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="2a59d-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="2a59d-183">Aby przeprowadzić sprawdzenia lub błędy pułapki dla hasła w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2a59d-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="2a59d-184">Utwórz pole zapasowe dla `Password` (`password` w poniższym przykładowym kodzie).</span><span class="sxs-lookup"><span data-stu-id="2a59d-184">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="2a59d-185">Wykonaj testy lub błędy pułapek w metodzie `Password` ustawiającej.</span><span class="sxs-lookup"><span data-stu-id="2a59d-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="2a59d-186">Poniższy przykład przedstawia natychmiastową opinię dla użytkownika, jeśli w wartości hasła jest używana spacja:</span><span class="sxs-lookup"><span data-stu-id="2a59d-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
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
        showPassword = !showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="2a59d-187">Przyciski radiowe</span><span class="sxs-lookup"><span data-stu-id="2a59d-187">Radio buttons</span></span>

<span data-ttu-id="2a59d-188">Aby uzyskać informacje na temat tworzenia powiązań z przyciskami radiowymi <xref:blazor/forms-validation#work-with-radio-buttons>w formularzu, zobacz.</span><span class="sxs-lookup"><span data-stu-id="2a59d-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
