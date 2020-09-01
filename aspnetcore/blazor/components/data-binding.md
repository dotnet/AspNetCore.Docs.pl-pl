---
title: ASP.NET Core Blazor powiązania danych
author: guardrex
description: Dowiedz się więcej o funkcjach powiązań danych dla składników i elementów modelu DOM w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
no-loc:
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
uid: blazor/components/data-binding
ms.openlocfilehash: d88cad10314872271250cd43212a64698f485381
ms.sourcegitcommit: 8ed9a413bdc2d665ad11add8828898d726ccb106
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280403"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="1175d-103">ASP.NET Core Blazor powiązania danych</span><span class="sxs-lookup"><span data-stu-id="1175d-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="1175d-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1175d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1175d-105">Razor składniki zapewniają funkcje powiązań danych za pośrednictwem atrybutu elementu HTML o nazwie [`@bind`](xref:mvc/views/razor#bind) z wartością pola, właściwości lub Razor wyrażenia.</span><span class="sxs-lookup"><span data-stu-id="1175d-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="1175d-106">Poniższy przykład wiąże `<input>` element z `currentValue` polem i `<input>` elementem `CurrentValue` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="1175d-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1175d-107">Gdy jeden z elementów niesie fokus, jego powiązane pole lub właściwość są aktualizowane.</span><span class="sxs-lookup"><span data-stu-id="1175d-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="1175d-108">Pole tekstowe jest aktualizowane w interfejsie użytkownika tylko wtedy, gdy składnik jest renderowany, a nie w odpowiedzi na zmianę wartości pola lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="1175d-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="1175d-109">Ponieważ składniki renderują się po wykonaniu kodu procedury obsługi zdarzeń, aktualizacje pól i właściwości są *zwykle* odzwierciedlane w interfejsie użytkownika natychmiast po wyzwoleniu programu obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="1175d-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="1175d-110">Używanie [`@bind`](xref:mvc/views/razor#bind) z `CurrentValue` właściwością ( `<input @bind="CurrentValue" />` ) jest zasadniczo równoważne z następującymi:</span><span class="sxs-lookup"><span data-stu-id="1175d-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1175d-111">Gdy składnik jest renderowany, `value` element wejściowy pochodzi z `CurrentValue` właściwości.</span><span class="sxs-lookup"><span data-stu-id="1175d-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="1175d-112">Gdy użytkownik wpisze w polu tekstowym i zmieni fokus elementu, `onchange` zdarzenie jest wywoływane i `CurrentValue` Właściwość jest ustawiana na wartość zmieniona.</span><span class="sxs-lookup"><span data-stu-id="1175d-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="1175d-113">W rzeczywistości generowanie kodu jest bardziej złożone niż to, ponieważ [`@bind`](xref:mvc/views/razor#bind) obsługuje przypadki, w których są wykonywane konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="1175d-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="1175d-114">W zasadzie [`@bind`](xref:mvc/views/razor#bind) kojarzy bieżącą wartość wyrażenia z `value` atrybutem i obsługuje zmiany przy użyciu zarejestrowanej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="1175d-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="1175d-115">Powiąż właściwość lub pole w innych zdarzeniach, dołączając także `@bind:event` atrybut z `event` parametrem.</span><span class="sxs-lookup"><span data-stu-id="1175d-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="1175d-116">Poniższy przykład wiąże `CurrentValue` Właściwość `oninput` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="1175d-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1175d-117">W przeciwieństwie do `onchange` , które jest wyzwalane, gdy element utraci fokus, `oninput` jest uruchamiany po zmianie wartości pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="1175d-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="1175d-118">W powiązaniu atrybutu rozróżniana jest wielkość liter:</span><span class="sxs-lookup"><span data-stu-id="1175d-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="1175d-119">`@bind` jest prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="1175d-119">`@bind` is valid.</span></span>
* <span data-ttu-id="1175d-120">`@Bind` i `@BIND` są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="1175d-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="1175d-121">Wartości niemożliwy do przeanalizowania</span><span class="sxs-lookup"><span data-stu-id="1175d-121">Unparsable values</span></span>

<span data-ttu-id="1175d-122">Gdy użytkownik dostarczy wartość niemożliwy do przeanalizowania do elementu powiązanego z danymi, wartość niemożliwy do przeanalizowania jest automatycznie przywracana do poprzedniej wartości po wyzwoleniu zdarzenia bind.</span><span class="sxs-lookup"><span data-stu-id="1175d-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="1175d-123">Poniżej przedstawiono przykładowy scenariusz:</span><span class="sxs-lookup"><span data-stu-id="1175d-123">Consider the following scenario:</span></span>

* <span data-ttu-id="1175d-124">`<input>`Element jest powiązany z `int` typem z początkową wartością `123` :</span><span class="sxs-lookup"><span data-stu-id="1175d-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="1175d-125">Użytkownik aktualizuje wartość elementu na `123.45` liście na stronie i zmienia fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="1175d-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="1175d-126">W poprzednim scenariuszu wartość elementu jest przywracana `123` .</span><span class="sxs-lookup"><span data-stu-id="1175d-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="1175d-127">Gdy wartość `123.45` zostanie odrzucona na korzyść oryginalnej wartości `123` , użytkownik rozumie, że ich wartość nie została zaakceptowana.</span><span class="sxs-lookup"><span data-stu-id="1175d-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="1175d-128">Domyślnie powiązanie dotyczy `onchange` zdarzenia elementu ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="1175d-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="1175d-129">Służy `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` do wyzwalania powiązań na innym zdarzeniu.</span><span class="sxs-lookup"><span data-stu-id="1175d-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="1175d-130">W przypadku `oninput` zdarzenia ( `@bind:event="oninput"` ) jego wersja następuje po naciśnięciu klawisza, które wprowadza niemożliwy do przeanalizowania wartość.</span><span class="sxs-lookup"><span data-stu-id="1175d-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="1175d-131">Podczas określania wartości docelowej dla `oninput` zdarzenia z `int` typem związanym użytkownik nie jest w trakcie wpisywania `.` znaku.</span><span class="sxs-lookup"><span data-stu-id="1175d-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="1175d-132">`.`Znak zostanie natychmiast usunięty, więc użytkownik otrzymuje natychmiastową opinię, że dozwolone są tylko liczby całkowite.</span><span class="sxs-lookup"><span data-stu-id="1175d-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="1175d-133">Istnieją scenariusze, w których przywrócenie wartości `oninput` zdarzenia nie jest idealne, na przykład wtedy, gdy użytkownik powinien mieć możliwość wyczyszczenia wartości, która nie może być przewidziana `<input>` .</span><span class="sxs-lookup"><span data-stu-id="1175d-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="1175d-134">Alternatywy obejmują:</span><span class="sxs-lookup"><span data-stu-id="1175d-134">Alternatives include:</span></span>

* <span data-ttu-id="1175d-135">Nie używaj `oninput` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="1175d-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="1175d-136">Użyj zdarzenia domyślnego `onchange` (tylko określenie `@bind="{PROPERTY OR FIELD}"` ), gdzie nie jest przywracana nieprawidłowa wartość, dopóki element nie utraci fokusu.</span><span class="sxs-lookup"><span data-stu-id="1175d-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="1175d-137">Powiąż z typem dopuszczającym wartość null, na przykład `int?` lub `string` i podaj logikę niestandardową do obsługi nieprawidłowych wpisów.</span><span class="sxs-lookup"><span data-stu-id="1175d-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="1175d-138">Użyj [składnika walidacji formularza](xref:blazor/forms-validation), takiego jak <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> lub <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="1175d-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="1175d-139">Składniki walidacji formularza mają wbudowaną obsługę zarządzania nieprawidłowymi danymi wejściowymi.</span><span class="sxs-lookup"><span data-stu-id="1175d-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="1175d-140">Aby uzyskać więcej informacji, zobacz <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="1175d-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="1175d-141">Składniki walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="1175d-141">Form validation components:</span></span>
  * <span data-ttu-id="1175d-142">Zezwalaj użytkownikowi na dostarczenie nieprawidłowych danych wejściowych i odbieranie błędów walidacji w skojarzonym <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="1175d-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="1175d-143">Wyświetlaj błędy walidacji w interfejsie użytkownika bez zakłócania wprowadzania dodatkowych danych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1175d-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="1175d-144">Ciągi formatujące</span><span class="sxs-lookup"><span data-stu-id="1175d-144">Format strings</span></span>

<span data-ttu-id="1175d-145">Powiązanie danych działa z <xref:System.DateTime> ciągami formatu przy użyciu `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="1175d-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="1175d-146">W tej chwili nie są dostępne inne wyrażenia formatu, takie jak formaty walutowe lub liczbowe.</span><span class="sxs-lookup"><span data-stu-id="1175d-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="1175d-147">W poprzednim kodzie `<input>` Typ pola () elementu jest `type` wartością domyślną `text` .</span><span class="sxs-lookup"><span data-stu-id="1175d-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="1175d-148">`@bind:format` jest obsługiwana w celu powiązania następujących typów .NET:</span><span class="sxs-lookup"><span data-stu-id="1175d-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="1175d-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="1175d-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="1175d-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="1175d-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="1175d-151">Ten `@bind:format` atrybut określa format daty, który ma zostać zastosowany do `value` `<input>` elementu.</span><span class="sxs-lookup"><span data-stu-id="1175d-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="1175d-152">Format jest również używany do analizowania wartości w przypadku `onchange` wystąpienia zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="1175d-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="1175d-153">Określanie formatu dla `date` typu pola nie jest zalecane, ponieważ Blazor ma wbudowaną obsługę formatowania dat.</span><span class="sxs-lookup"><span data-stu-id="1175d-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="1175d-154">Pomimo zalecenia, należy używać tylko `yyyy-MM-dd` formatu daty dla powiązania, aby prawidłowo działać, jeśli format jest dostarczany z `date` typem pola:</span><span class="sxs-lookup"><span data-stu-id="1175d-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="1175d-155">Powiązanie element nadrzędny-to-Child z parametrami składnika</span><span class="sxs-lookup"><span data-stu-id="1175d-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="1175d-156">Parametry składnika umożliwiają powiązanie właściwości i pól składnika nadrzędnego z `@bind-{PROPERTY OR FIELD}` składnią.</span><span class="sxs-lookup"><span data-stu-id="1175d-156">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="1175d-157">Następujący `Child` składnik ( `Child.razor` ) ma `Year` parametr składnika i `YearChanged` wywołanie zwrotne:</span><span class="sxs-lookup"><span data-stu-id="1175d-157">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

<span data-ttu-id="1175d-158">Wywołanie zwrotne ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) musi być nazwane jako nazwa parametru składnika, po którym następuje `Changed` sufiks "" `{PARAMETER NAME}Changed` .</span><span class="sxs-lookup"><span data-stu-id="1175d-158">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="1175d-159">W poprzednim przykładzie wywołanie zwrotne ma nazwę `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="1175d-159">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="1175d-160">Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Components.EventCallback%601> , zobacz <xref:blazor/components/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="1175d-160">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="1175d-161">W poniższym `Parent` składniku ( `Parent.razor` ) `year` pole jest powiązane z `Year` parametrem składnika podrzędnego:</span><span class="sxs-lookup"><span data-stu-id="1175d-161">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="1175d-162">`Year`Parametr jest możliwy do powiązania, ponieważ ma zdarzenie towarzyszące `YearChanged` pasujące do typu `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="1175d-162">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="1175d-163">Zgodnie z Konwencją Właściwość można powiązać z odpowiadającą jej obsługą zdarzeń, dołączając `@bind-{PROPERTY}:event` atrybut przypisany do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="1175d-163">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="1175d-164">`<Child @bind-Year="year" />` jest odpowiednikiem zapisu:</span><span class="sxs-lookup"><span data-stu-id="1175d-164">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="1175d-165">Powiązanie elementu podrzędnego z elementem nadrzędnym z powiązaniem łańcuchowym</span><span class="sxs-lookup"><span data-stu-id="1175d-165">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="1175d-166">Typowy scenariusz polega na łańcuchu parametru powiązanego z danymi do elementu strony w danych wyjściowych składnika.</span><span class="sxs-lookup"><span data-stu-id="1175d-166">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="1175d-167">Ten scenariusz jest nazywany *powiązaniem łańcuchowym* , ponieważ wiele poziomów powiązań występuje jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="1175d-167">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="1175d-168">Nie można zaimplementować powiązania łańcuchowego ze [`@bind`](xref:mvc/views/razor#bind) składnią w składniku podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="1175d-168">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="1175d-169">Program obsługi zdarzeń i wartość muszą być określone osobno.</span><span class="sxs-lookup"><span data-stu-id="1175d-169">The event handler and value must be specified separately.</span></span> <span data-ttu-id="1175d-170">Składnik nadrzędny, jednak może używać [`@bind`](xref:mvc/views/razor#bind) składni z parametrem składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="1175d-170">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="1175d-171">Następujący `PasswordField` składnik ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="1175d-171">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="1175d-172">Ustawia `<input>` wartość elementu na `Password` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="1175d-172">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="1175d-173">Uwidacznia zmiany `Password` właściwości w składniku nadrzędnym z [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="1175d-173">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="1175d-174">Używa `onclick` zdarzenia do wyzwolenia `ToggleShowPassword` metody.</span><span class="sxs-lookup"><span data-stu-id="1175d-174">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="1175d-175">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="1175d-175">For more information, see <xref:blazor/components/event-handling>.</span></span>

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

<span data-ttu-id="1175d-176">`PasswordField`Składnik jest używany w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="1175d-176">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="1175d-177">Aby przeprowadzić sprawdzenia lub błędy pułapki dla hasła w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1175d-177">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="1175d-178">Utwórz pole zapasowe dla `Password` ( `password` w poniższym przykładowym kodzie).</span><span class="sxs-lookup"><span data-stu-id="1175d-178">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="1175d-179">Wykonaj testy lub błędy pułapek w metodzie `Password` ustawiającej.</span><span class="sxs-lookup"><span data-stu-id="1175d-179">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="1175d-180">Poniższy przykład przedstawia natychmiastową opinię dla użytkownika, jeśli w wartości hasła jest używana spacja:</span><span class="sxs-lookup"><span data-stu-id="1175d-180">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="1175d-181">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1175d-181">Additional resources</span></span>

* [<span data-ttu-id="1175d-182">Powiązywanie z przyciskami radiowymi w formularzu</span><span class="sxs-lookup"><span data-stu-id="1175d-182">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="1175d-183">Powiązywanie `<select>` opcji elementu z wartościami obiektów C# `null` w formularzu</span><span class="sxs-lookup"><span data-stu-id="1175d-183">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
