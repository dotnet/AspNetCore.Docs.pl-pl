---
title: ASP.NET Core Blazor powiązania danych
author: guardrex
description: Dowiedz się więcej o funkcjach powiązań danych dla składników i elementów modelu DOM w Blazor aplikacjach.
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
ms.openlocfilehash: 3b419b4738bd3f434cf4a9d8ccdf3b3af86ba1d5
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "83998500"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="dfeee-103">ASP.NET Core Blazor powiązania danych</span><span class="sxs-lookup"><span data-stu-id="dfeee-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="dfeee-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="dfeee-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="dfeee-105">składniki zapewniają funkcje powiązań danych za pośrednictwem atrybutu elementu HTML o nazwie [`@bind`](xref:mvc/views/razor#bind) z wartością pola, właściwości lub Razor wyrażenia.</span><span class="sxs-lookup"><span data-stu-id="dfeee-105"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="dfeee-106">Poniższy przykład wiąże `CurrentValue` Właściwość z wartością pola tekstowego:</span><span class="sxs-lookup"><span data-stu-id="dfeee-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="dfeee-107">Gdy pole tekstowe utraci fokus, wartość właściwości jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="dfeee-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="dfeee-108">Pole tekstowe jest aktualizowane w interfejsie użytkownika tylko wtedy, gdy składnik jest renderowany, a nie w odpowiedzi na zmianę wartości właściwości.</span><span class="sxs-lookup"><span data-stu-id="dfeee-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="dfeee-109">Ponieważ składniki renderują się po wykonaniu kodu procedury obsługi zdarzeń, aktualizacje właściwości są *zwykle* odzwierciedlane w interfejsie użytkownika natychmiast po wyzwoleniu programu obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="dfeee-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="dfeee-110">Używanie [`@bind`](xref:mvc/views/razor#bind) z `CurrentValue` właściwością ( `<input @bind="CurrentValue" />` ) jest zasadniczo równoważne z następującymi:</span><span class="sxs-lookup"><span data-stu-id="dfeee-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="dfeee-111">Gdy składnik jest renderowany, `value` element wejściowy pochodzi z `CurrentValue` właściwości.</span><span class="sxs-lookup"><span data-stu-id="dfeee-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="dfeee-112">Gdy użytkownik wpisze w polu tekstowym i zmieni fokus elementu, `onchange` zdarzenie jest wywoływane i `CurrentValue` Właściwość jest ustawiana na wartość zmieniona.</span><span class="sxs-lookup"><span data-stu-id="dfeee-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="dfeee-113">W rzeczywistości generowanie kodu jest bardziej skomplikowane, ponieważ [`@bind`](xref:mvc/views/razor#bind) obsługuje przypadki, w których są wykonywane konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="dfeee-113">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="dfeee-114">W zasadzie [`@bind`](xref:mvc/views/razor#bind) kojarzy bieżącą wartość wyrażenia z `value` atrybutem i obsługuje zmiany przy użyciu zarejestrowanej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="dfeee-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="dfeee-115">Powiąż właściwość lub pole w innych zdarzeniach, dołączając także `@bind:event` atrybut z `event` parametrem.</span><span class="sxs-lookup"><span data-stu-id="dfeee-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="dfeee-116">Poniższy przykład wiąże `CurrentValue` Właściwość `oninput` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="dfeee-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="dfeee-117">W przeciwieństwie do `onchange` , które jest wyzwalane, gdy element utraci fokus, `oninput` jest uruchamiany po zmianie wartości pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="dfeee-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="dfeee-118">Użyj `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` składni with, aby powiązać atrybuty elementu inne niż `value` .</span><span class="sxs-lookup"><span data-stu-id="dfeee-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="dfeee-119">W poniższym przykładzie styl akapitu zostanie zaktualizowany po `paragraphStyle` zmianie wartości:</span><span class="sxs-lookup"><span data-stu-id="dfeee-119">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

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

<span data-ttu-id="dfeee-120">W powiązaniu atrybutu rozróżniana jest wielkość liter:</span><span class="sxs-lookup"><span data-stu-id="dfeee-120">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="dfeee-121">`@bind`jest prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="dfeee-121">`@bind` is valid.</span></span>
* <span data-ttu-id="dfeee-122">`@Bind`i `@BIND` są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="dfeee-122">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="dfeee-123">Wartości niemożliwy do przeanalizowania</span><span class="sxs-lookup"><span data-stu-id="dfeee-123">Unparsable values</span></span>

<span data-ttu-id="dfeee-124">Gdy użytkownik dostarczy wartość niemożliwy do przeanalizowania do elementu powiązanego z danymi, wartość niemożliwy do przeanalizowania jest automatycznie przywracana do poprzedniej wartości po wyzwoleniu zdarzenia bind.</span><span class="sxs-lookup"><span data-stu-id="dfeee-124">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="dfeee-125">Poniżej przedstawiono przykładowy scenariusz:</span><span class="sxs-lookup"><span data-stu-id="dfeee-125">Consider the following scenario:</span></span>

* <span data-ttu-id="dfeee-126">`<input>`Element jest powiązany z `int` typem z początkową wartością `123` :</span><span class="sxs-lookup"><span data-stu-id="dfeee-126">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="dfeee-127">Użytkownik aktualizuje wartość elementu na `123.45` liście na stronie i zmienia fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="dfeee-127">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="dfeee-128">W poprzednim scenariuszu wartość elementu jest przywracana `123` .</span><span class="sxs-lookup"><span data-stu-id="dfeee-128">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="dfeee-129">Gdy wartość `123.45` zostanie odrzucona na korzyść oryginalnej wartości `123` , użytkownik rozumie, że ich wartość nie została zaakceptowana.</span><span class="sxs-lookup"><span data-stu-id="dfeee-129">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="dfeee-130">Domyślnie powiązanie dotyczy `onchange` zdarzenia elementu ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="dfeee-130">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="dfeee-131">Służy `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` do wyzwalania powiązań na innym zdarzeniu.</span><span class="sxs-lookup"><span data-stu-id="dfeee-131">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="dfeee-132">W przypadku `oninput` zdarzenia ( `@bind:event="oninput"` ) jego wersja następuje po naciśnięciu klawisza, które wprowadza niemożliwy do przeanalizowania wartość.</span><span class="sxs-lookup"><span data-stu-id="dfeee-132">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="dfeee-133">Podczas określania wartości docelowej dla `oninput` zdarzenia z `int` typem związanym użytkownik nie jest w trakcie wpisywania `.` znaku.</span><span class="sxs-lookup"><span data-stu-id="dfeee-133">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="dfeee-134">`.`Znak zostanie natychmiast usunięty, więc użytkownik otrzymuje natychmiastową opinię, że dozwolone są tylko liczby całkowite.</span><span class="sxs-lookup"><span data-stu-id="dfeee-134">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="dfeee-135">Istnieją scenariusze, w których przywrócenie wartości `oninput` zdarzenia nie jest idealne, na przykład wtedy, gdy użytkownik powinien mieć możliwość wyczyszczenia wartości, która nie może być przewidziana `<input>` .</span><span class="sxs-lookup"><span data-stu-id="dfeee-135">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="dfeee-136">Alternatywy obejmują:</span><span class="sxs-lookup"><span data-stu-id="dfeee-136">Alternatives include:</span></span>

* <span data-ttu-id="dfeee-137">Nie używaj `oninput` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="dfeee-137">Don't use the `oninput` event.</span></span> <span data-ttu-id="dfeee-138">Użyj zdarzenia domyślnego `onchange` (tylko określenie `@bind="{PROPERTY OR FIELD}"` ), gdzie nie jest przywracana nieprawidłowa wartość, dopóki element nie utraci fokusu.</span><span class="sxs-lookup"><span data-stu-id="dfeee-138">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="dfeee-139">Powiąż z typem dopuszczającym wartość null, taką jak `int?` lub `string` , i podaj logikę niestandardową do obsługi nieprawidłowych wpisów.</span><span class="sxs-lookup"><span data-stu-id="dfeee-139">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="dfeee-140">Użyj [składnika walidacji formularza](xref:blazor/forms-validation), takiego jak <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> lub <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="dfeee-140">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="dfeee-141">Składniki walidacji formularza mają wbudowaną obsługę zarządzania nieprawidłowymi danymi wejściowymi.</span><span class="sxs-lookup"><span data-stu-id="dfeee-141">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="dfeee-142">Składniki walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="dfeee-142">Form validation components:</span></span>
  * <span data-ttu-id="dfeee-143">Zezwalaj użytkownikowi na dostarczenie nieprawidłowych danych wejściowych i odbieranie błędów walidacji w skojarzonym <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="dfeee-143">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="dfeee-144">Wyświetlaj błędy walidacji w interfejsie użytkownika bez zakłócania wprowadzania dodatkowych danych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dfeee-144">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="dfeee-145">Ciągi formatujące</span><span class="sxs-lookup"><span data-stu-id="dfeee-145">Format strings</span></span>

<span data-ttu-id="dfeee-146">Powiązanie danych działa z <xref:System.DateTime> ciągami formatu przy użyciu `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="dfeee-146">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="dfeee-147">W tej chwili nie są dostępne inne wyrażenia formatu, takie jak formaty walutowe lub liczbowe.</span><span class="sxs-lookup"><span data-stu-id="dfeee-147">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="dfeee-148">W poprzednim kodzie `<input>` Typ pola () elementu jest `type` wartością domyślną `text` .</span><span class="sxs-lookup"><span data-stu-id="dfeee-148">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="dfeee-149">`@bind:format`jest obsługiwana w celu powiązania następujących typów .NET:</span><span class="sxs-lookup"><span data-stu-id="dfeee-149">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="dfeee-150"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="dfeee-150"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="dfeee-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="dfeee-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="dfeee-152">Ten `@bind:format` atrybut określa format daty, który ma zostać zastosowany do `value` `<input>` elementu.</span><span class="sxs-lookup"><span data-stu-id="dfeee-152">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="dfeee-153">Format jest również używany do analizowania wartości w przypadku `onchange` wystąpienia zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="dfeee-153">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="dfeee-154">Określanie formatu dla `date` typu pola nie jest zalecane, ponieważ Blazor ma wbudowaną obsługę formatowania dat.</span><span class="sxs-lookup"><span data-stu-id="dfeee-154">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="dfeee-155">Pomimo zalecenia, należy używać tylko `yyyy-MM-dd` formatu daty do poprawnego działania, jeśli format jest dostarczany z `date` typem pola:</span><span class="sxs-lookup"><span data-stu-id="dfeee-155">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="dfeee-156">Powiązanie element nadrzędny-to-Child z parametrami składnika</span><span class="sxs-lookup"><span data-stu-id="dfeee-156">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="dfeee-157">Powiązanie rozpoznaje parametry składnika, gdzie `@bind-{PROPERTY}` można powiązać wartość właściwości z składnika nadrzędnego w dół ze składnikiem podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="dfeee-157">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="dfeee-158">Powiązanie z elementu podrzędnego z elementem nadrzędnym jest omówione w [powiązaniu podrzędnie-to-Parent z częściowym powiązaniem powiązania](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="dfeee-158">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="dfeee-159">Następujący składnik podrzędny ( `ChildComponent` ) ma `Year` parametr składnika i `YearChanged` wywołanie zwrotne:</span><span class="sxs-lookup"><span data-stu-id="dfeee-159">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="dfeee-160"><xref:Microsoft.AspNetCore.Components.EventCallback%601>wyjaśniono w temacie <xref:blazor/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="dfeee-160"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="dfeee-161">Poniższy składnik nadrzędny używa:</span><span class="sxs-lookup"><span data-stu-id="dfeee-161">The following parent component uses:</span></span>

* <span data-ttu-id="dfeee-162">`ChildComponent`i wiąże `ParentYear` parametr z elementu nadrzędnego z `Year` parametrem w składniku podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="dfeee-162">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="dfeee-163">To `onclick` zdarzenie służy do wyzwalania `ChangeTheYear` metody.</span><span class="sxs-lookup"><span data-stu-id="dfeee-163">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="dfeee-164">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="dfeee-164">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="dfeee-165">Załadowanie `ParentComponent` powoduje utworzenie następującej adjustacji:</span><span class="sxs-lookup"><span data-stu-id="dfeee-165">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="dfeee-166">Jeśli wartość `ParentYear` właściwości zostanie zmieniona przez wybranie przycisku w `ParentComponent` , `Year` Właściwość `ChildComponent` jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="dfeee-166">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="dfeee-167">Nowa wartość `Year` jest renderowana w interfejsie użytkownika podczas jego `ParentComponent` renderowania:</span><span class="sxs-lookup"><span data-stu-id="dfeee-167">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="dfeee-168">`Year`Parametr jest możliwy do powiązania, ponieważ ma zdarzenie towarzyszące `YearChanged` pasujące do typu `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="dfeee-168">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="dfeee-169">Zgodnie z Konwencją, `<ChildComponent @bind-Year="ParentYear" />` jest zasadniczo równoważne zapisowi:</span><span class="sxs-lookup"><span data-stu-id="dfeee-169">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="dfeee-170">Ogólnie rzecz biorąc, właściwość może być powiązana z odpowiadającą jej obsługą zdarzeń przez uwzględnienie `@bind-{PROPRETY}:event` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="dfeee-170">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="dfeee-171">Na przykład właściwość `MyProp` może być powiązana z `MyEventHandler` użyciem następujących dwóch atrybutów:</span><span class="sxs-lookup"><span data-stu-id="dfeee-171">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="dfeee-172">Powiązanie elementu podrzędnego z elementem nadrzędnym z powiązaniem łańcuchowym</span><span class="sxs-lookup"><span data-stu-id="dfeee-172">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="dfeee-173">Typowy scenariusz polega na łańcuchu parametru powiązanego z danymi do elementu strony w danych wyjściowych składnika.</span><span class="sxs-lookup"><span data-stu-id="dfeee-173">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="dfeee-174">Ten scenariusz jest nazywany *powiązaniem łańcuchowym* , ponieważ wiele poziomów powiązań występuje jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="dfeee-174">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="dfeee-175">Nie można zaimplementować powiązania łańcuchowego przy użyciu [`@bind`](xref:mvc/views/razor#bind) składni w elemencie strony.</span><span class="sxs-lookup"><span data-stu-id="dfeee-175">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="dfeee-176">Program obsługi zdarzeń i wartość muszą być określone osobno.</span><span class="sxs-lookup"><span data-stu-id="dfeee-176">The event handler and value must be specified separately.</span></span> <span data-ttu-id="dfeee-177">Składnik nadrzędny, jednak może używać [`@bind`](xref:mvc/views/razor#bind) składni z parametrem składnika.</span><span class="sxs-lookup"><span data-stu-id="dfeee-177">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="dfeee-178">Następujący `PasswordField` składnik (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="dfeee-178">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="dfeee-179">Ustawia `<input>` wartość elementu na `Password` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="dfeee-179">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="dfeee-180">Uwidacznia zmiany `Password` właściwości w składniku nadrzędnym z [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="dfeee-180">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="dfeee-181">Używa `onclick` zdarzenia, aby wyzwolić `ToggleShowPassword` metodę.</span><span class="sxs-lookup"><span data-stu-id="dfeee-181">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="dfeee-182">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="dfeee-182">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="dfeee-183">`PasswordField`Składnik jest używany w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="dfeee-183">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="dfeee-184">Aby przeprowadzić sprawdzenia lub błędy pułapki dla hasła w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="dfeee-184">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="dfeee-185">Utwórz pole zapasowe dla `Password` ( `password` w poniższym przykładowym kodzie).</span><span class="sxs-lookup"><span data-stu-id="dfeee-185">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="dfeee-186">Wykonaj testy lub błędy pułapek w metodzie `Password` ustawiającej.</span><span class="sxs-lookup"><span data-stu-id="dfeee-186">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="dfeee-187">Poniższy przykład przedstawia natychmiastową opinię dla użytkownika, jeśli w wartości hasła jest używana spacja:</span><span class="sxs-lookup"><span data-stu-id="dfeee-187">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="dfeee-188">Przyciski radiowe</span><span class="sxs-lookup"><span data-stu-id="dfeee-188">Radio buttons</span></span>

<span data-ttu-id="dfeee-189">Aby uzyskać informacje na temat tworzenia powiązań z przyciskami radiowymi w formularzu, zobacz <xref:blazor/forms-validation#work-with-radio-buttons> .</span><span class="sxs-lookup"><span data-stu-id="dfeee-189">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
