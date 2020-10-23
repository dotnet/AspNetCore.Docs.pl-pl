---
title: ASP.NET Core Blazor powiązania danych
author: guardrex
description: Dowiedz się więcej o funkcjach powiązań danych dla składników i elementów modelu DOM w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
ms.openlocfilehash: 5a4d50d88ebdf606da397666bf3003232cddd955
ms.sourcegitcommit: d84a225ec3381355c343460deed50f2fa5722f60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92429102"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="cba72-103">ASP.NET Core Blazor powiązania danych</span><span class="sxs-lookup"><span data-stu-id="cba72-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="cba72-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="cba72-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="cba72-105">Razor składniki zapewniają funkcje powiązań danych za pośrednictwem atrybutu elementu HTML o nazwie [`@bind`](xref:mvc/views/razor#bind) z wartością pola, właściwości lub Razor wyrażenia.</span><span class="sxs-lookup"><span data-stu-id="cba72-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="cba72-106">Poniższy przykład wiąże `<input>` element z `currentValue` polem i `<input>` elementem `CurrentValue` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="cba72-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="cba72-107">Gdy jeden z elementów utraci fokus, jego powiązane pole lub właściwość jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="cba72-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="cba72-108">Pole tekstowe jest aktualizowane w interfejsie użytkownika tylko wtedy, gdy składnik jest renderowany, a nie w odpowiedzi na zmianę wartości pola lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="cba72-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="cba72-109">Ponieważ składniki renderują się po wykonaniu kodu procedury obsługi zdarzeń, aktualizacje pól i właściwości są *zwykle* odzwierciedlane w interfejsie użytkownika natychmiast po wyzwoleniu programu obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="cba72-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="cba72-110">Używanie [`@bind`](xref:mvc/views/razor#bind) z `CurrentValue` właściwością ( `<input @bind="CurrentValue" />` ) jest zasadniczo równoważne z następującymi:</span><span class="sxs-lookup"><span data-stu-id="cba72-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="cba72-111">Gdy składnik jest renderowany, `value` element wejściowy pochodzi z `CurrentValue` właściwości.</span><span class="sxs-lookup"><span data-stu-id="cba72-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="cba72-112">Gdy użytkownik wpisze w polu tekstowym i zmieni fokus elementu, `onchange` zdarzenie jest wywoływane i `CurrentValue` Właściwość jest ustawiana na wartość zmieniona.</span><span class="sxs-lookup"><span data-stu-id="cba72-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="cba72-113">W rzeczywistości generowanie kodu jest bardziej złożone niż to, ponieważ [`@bind`](xref:mvc/views/razor#bind) obsługuje przypadki, w których są wykonywane konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="cba72-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="cba72-114">W zasadzie [`@bind`](xref:mvc/views/razor#bind) kojarzy bieżącą wartość wyrażenia z `value` atrybutem i obsługuje zmiany przy użyciu zarejestrowanej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="cba72-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="cba72-115">Powiąż właściwość lub pole w innych zdarzeniach, dołączając także `@bind:event` atrybut z `event` parametrem.</span><span class="sxs-lookup"><span data-stu-id="cba72-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="cba72-116">Poniższy przykład wiąże `CurrentValue` Właściwość `oninput` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="cba72-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="cba72-117">W przeciwieństwie do `onchange` , które jest wyzwalane, gdy element utraci fokus, `oninput` jest uruchamiany po zmianie wartości pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="cba72-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="cba72-118">W powiązaniu atrybutu rozróżniana jest wielkość liter:</span><span class="sxs-lookup"><span data-stu-id="cba72-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="cba72-119">`@bind` jest prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="cba72-119">`@bind` is valid.</span></span>
* <span data-ttu-id="cba72-120">`@Bind` i `@BIND` są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="cba72-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="cba72-121">Wartości niemożliwy do przeanalizowania</span><span class="sxs-lookup"><span data-stu-id="cba72-121">Unparsable values</span></span>

<span data-ttu-id="cba72-122">Gdy użytkownik dostarczy wartość niemożliwy do przeanalizowania do elementu powiązanego z danymi, wartość niemożliwy do przeanalizowania jest automatycznie przywracana do poprzedniej wartości po wyzwoleniu zdarzenia bind.</span><span class="sxs-lookup"><span data-stu-id="cba72-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="cba72-123">Poniżej przedstawiono przykładowy scenariusz:</span><span class="sxs-lookup"><span data-stu-id="cba72-123">Consider the following scenario:</span></span>

* <span data-ttu-id="cba72-124">`<input>`Element jest powiązany z `int` typem z początkową wartością `123` :</span><span class="sxs-lookup"><span data-stu-id="cba72-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="cba72-125">Użytkownik aktualizuje wartość elementu na `123.45` liście na stronie i zmienia fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="cba72-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="cba72-126">W poprzednim scenariuszu wartość elementu jest przywracana `123` .</span><span class="sxs-lookup"><span data-stu-id="cba72-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="cba72-127">Gdy wartość `123.45` zostanie odrzucona na korzyść oryginalnej wartości `123` , użytkownik rozumie, że ich wartość nie została zaakceptowana.</span><span class="sxs-lookup"><span data-stu-id="cba72-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="cba72-128">Domyślnie powiązanie dotyczy `onchange` zdarzenia elementu ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="cba72-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="cba72-129">Służy `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` do wyzwalania powiązań na innym zdarzeniu.</span><span class="sxs-lookup"><span data-stu-id="cba72-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="cba72-130">W przypadku `oninput` zdarzenia ( `@bind:event="oninput"` ) jego wersja następuje po naciśnięciu klawisza, które wprowadza niemożliwy do przeanalizowania wartość.</span><span class="sxs-lookup"><span data-stu-id="cba72-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="cba72-131">Podczas określania wartości docelowej dla `oninput` zdarzenia z `int` typem związanym użytkownik nie jest w trakcie wpisywania `.` znaku.</span><span class="sxs-lookup"><span data-stu-id="cba72-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="cba72-132">`.`Znak zostanie natychmiast usunięty, więc użytkownik otrzymuje natychmiastową opinię, że dozwolone są tylko liczby całkowite.</span><span class="sxs-lookup"><span data-stu-id="cba72-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="cba72-133">Istnieją scenariusze, w których przywrócenie wartości `oninput` zdarzenia nie jest idealne, na przykład wtedy, gdy użytkownik powinien mieć możliwość wyczyszczenia wartości, która nie może być przewidziana `<input>` .</span><span class="sxs-lookup"><span data-stu-id="cba72-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="cba72-134">Alternatywy obejmują:</span><span class="sxs-lookup"><span data-stu-id="cba72-134">Alternatives include:</span></span>

* <span data-ttu-id="cba72-135">Nie używaj `oninput` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="cba72-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="cba72-136">Użyj zdarzenia domyślnego `onchange` (tylko określenie `@bind="{PROPERTY OR FIELD}"` ), gdzie nie jest przywracana nieprawidłowa wartość, dopóki element nie utraci fokusu.</span><span class="sxs-lookup"><span data-stu-id="cba72-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="cba72-137">Powiąż z typem dopuszczającym wartość null, na przykład `int?` lub `string` i podaj logikę niestandardową do obsługi nieprawidłowych wpisów.</span><span class="sxs-lookup"><span data-stu-id="cba72-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="cba72-138">Użyj [składnika walidacji formularza](xref:blazor/forms-validation), takiego jak <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> lub <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="cba72-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="cba72-139">Składniki walidacji formularza mają wbudowaną obsługę zarządzania nieprawidłowymi danymi wejściowymi.</span><span class="sxs-lookup"><span data-stu-id="cba72-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="cba72-140">Aby uzyskać więcej informacji, zobacz <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="cba72-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="cba72-141">Składniki walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="cba72-141">Form validation components:</span></span>
  * <span data-ttu-id="cba72-142">Zezwalaj użytkownikowi na dostarczenie nieprawidłowych danych wejściowych i odbieranie błędów walidacji w skojarzonym <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="cba72-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="cba72-143">Wyświetlaj błędy walidacji w interfejsie użytkownika bez zakłócania wprowadzania dodatkowych danych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cba72-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="cba72-144">Ciągi formatujące</span><span class="sxs-lookup"><span data-stu-id="cba72-144">Format strings</span></span>

<span data-ttu-id="cba72-145">Powiązanie danych działa z <xref:System.DateTime> ciągami formatu przy użyciu `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="cba72-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="cba72-146">W tej chwili nie są dostępne inne wyrażenia formatu, takie jak formaty walutowe lub liczbowe.</span><span class="sxs-lookup"><span data-stu-id="cba72-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="cba72-147">W poprzednim kodzie `<input>` Typ pola () elementu jest `type` wartością domyślną `text` .</span><span class="sxs-lookup"><span data-stu-id="cba72-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="cba72-148">`@bind:format` jest obsługiwana w celu powiązania następujących typów .NET:</span><span class="sxs-lookup"><span data-stu-id="cba72-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="cba72-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="cba72-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="cba72-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="cba72-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="cba72-151">Ten `@bind:format` atrybut określa format daty, który ma zostać zastosowany do `value` `<input>` elementu.</span><span class="sxs-lookup"><span data-stu-id="cba72-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="cba72-152">Format jest również używany do analizowania wartości w przypadku `onchange` wystąpienia zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="cba72-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="cba72-153">Określanie formatu dla `date` typu pola nie jest zalecane, ponieważ Blazor ma wbudowaną obsługę formatowania dat.</span><span class="sxs-lookup"><span data-stu-id="cba72-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="cba72-154">Pomimo zalecenia, należy używać tylko `yyyy-MM-dd` formatu daty dla powiązania, aby prawidłowo działać, jeśli format jest dostarczany z `date` typem pola:</span><span class="sxs-lookup"><span data-stu-id="cba72-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a><span data-ttu-id="cba72-155">Powiązanie z parametrami składnika</span><span class="sxs-lookup"><span data-stu-id="cba72-155">Binding with component parameters</span></span>

<span data-ttu-id="cba72-156">Typowy scenariusz polega na powiązaniu właściwości w składniku podrzędnym z właściwością w jej elemencie nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="cba72-156">A common scenario is binding a property in a child component to a property in its parent.</span></span> <span data-ttu-id="cba72-157">Ten scenariusz jest nazywany *powiązaniem łańcuchowym* , ponieważ wiele poziomów powiązań występuje jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="cba72-157">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="cba72-158">Parametry składnika umożliwiają powiązanie właściwości i pól składnika nadrzędnego z `@bind-{PROPERTY OR FIELD}` składnią.</span><span class="sxs-lookup"><span data-stu-id="cba72-158">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="cba72-159">Nie można zaimplementować powiązań łańcuchowych przy użyciu [`@bind`](xref:mvc/views/razor#bind) składni w składniku podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="cba72-159">Chained binds can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="cba72-160">Procedura obsługi zdarzeń i wartość musi być określona oddzielnie, aby można było obsługiwać aktualizowanie właściwości w elemencie nadrzędnym ze składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="cba72-160">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="cba72-161">Składnik nadrzędny nadal wykorzystuje [`@bind`](xref:mvc/views/razor#bind) składnię w celu skonfigurowania powiązania danych ze składnikiem podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="cba72-161">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the data-binding with the child component.</span></span>

<span data-ttu-id="cba72-162">Następujący `Child` składnik ( `Shared/Child.razor` ) ma `Year` parametr składnika i `YearChanged` wywołanie zwrotne:</span><span class="sxs-lookup"><span data-stu-id="cba72-162">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

<span data-ttu-id="cba72-163">Wywołanie zwrotne ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) musi być nazwane jako nazwa parametru składnika, po którym następuje `Changed` sufiks "" `{PARAMETER NAME}Changed` .</span><span class="sxs-lookup"><span data-stu-id="cba72-163">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="cba72-164">W poprzednim przykładzie wywołanie zwrotne ma nazwę `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="cba72-164">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="cba72-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> Wywołuje delegata skojarzonego z powiązaniem z podanym argumentem i wysyła powiadomienie o zdarzeniu dla zmienionej właściwości.</span><span class="sxs-lookup"><span data-stu-id="cba72-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="cba72-166">W poniższym `Parent` składniku ( `Parent.razor` ) `year` pole jest powiązane z `Year` parametrem składnika podrzędnego:</span><span class="sxs-lookup"><span data-stu-id="cba72-166">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="cba72-167">`Year`Parametr jest możliwy do powiązania, ponieważ ma zdarzenie towarzyszące `YearChanged` pasujące do typu `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="cba72-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="cba72-168">Zgodnie z Konwencją Właściwość można powiązać z odpowiadającą jej obsługą zdarzeń, dołączając `@bind-{PROPERTY}:event` atrybut przypisany do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="cba72-168">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="cba72-169">`<Child @bind-Year="year" />` jest odpowiednikiem zapisu:</span><span class="sxs-lookup"><span data-stu-id="cba72-169">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="cba72-170">W bardziej zaawansowanym i rzeczywistym przykładzie Poniższy `PasswordField` składnik ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="cba72-170">In a more sophisticated and real-world example, the following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="cba72-171">Ustawia `<input>` wartość elementu na `password` pole.</span><span class="sxs-lookup"><span data-stu-id="cba72-171">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="cba72-172">Uwidacznia zmiany właściwości w `Password` składniku nadrzędnym [`EventCallback`](xref:blazor/components/event-handling#eventcallback) , który przekazuje w bieżącej wartości pola elementu podrzędnego `password` jako argument.</span><span class="sxs-lookup"><span data-stu-id="cba72-172">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="cba72-173">Używa `onclick` zdarzenia do wyzwolenia `ToggleShowPassword` metody.</span><span class="sxs-lookup"><span data-stu-id="cba72-173">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="cba72-174">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="cba72-174">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="cba72-175">`PasswordField`Składnik jest używany w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="cba72-175">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="cba72-176">Wykonaj sprawdzenia lub błędy pułapki w metodzie, która wywołuje delegata powiązania.</span><span class="sxs-lookup"><span data-stu-id="cba72-176">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="cba72-177">Poniższy przykład przedstawia natychmiastową opinię dla użytkownika, jeśli w wartości hasła jest używana spacja:</span><span class="sxs-lookup"><span data-stu-id="cba72-177">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();
        
        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="cba72-178">Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Components.EventCallback%601> , zobacz <xref:blazor/components/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="cba72-178">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="cba72-179">Powiązywanie w więcej niż dwóch składnikach</span><span class="sxs-lookup"><span data-stu-id="cba72-179">Bind across more than two components</span></span>

<span data-ttu-id="cba72-180">Można powiązać dowolną liczbę składników zagnieżdżonych, ale należy przestrzegać jednokierunkowego przepływu danych:</span><span class="sxs-lookup"><span data-stu-id="cba72-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="cba72-181">Powiadomienia o zmianach *przepływają w górę hierarchii*.</span><span class="sxs-lookup"><span data-stu-id="cba72-181">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="cba72-182">Nowe wartości parametrów *przepływają w dół hierarchii*.</span><span class="sxs-lookup"><span data-stu-id="cba72-182">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="cba72-183">Typowym i Zalecanym podejściem jest przechowywanie wyłącznie danych źródłowych w składniku nadrzędnym, aby uniknąć wszelkich pomyłek dotyczących stanu, który należy zaktualizować.</span><span class="sxs-lookup"><span data-stu-id="cba72-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="cba72-184">Poniższe składniki przedstawiają powyższe koncepcje:</span><span class="sxs-lookup"><span data-stu-id="cba72-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="cba72-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="cba72-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Property: <b>@parentValue</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-Property="parentValue" />

@code {
    private string parentValue = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentValue = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="cba72-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="cba72-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-Property="BoundValue" />
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private string BoundValue
    {
        get => Property;
        set => PropertyChanged.InvokeAsync(value);
    }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="cba72-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="cba72-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="cba72-188">W przypadku alternatywnego podejścia przystosowanego do udostępniania danych w pamięci między składnikami, które nie są niekoniecznie zagnieżdżone, zobacz <xref:blazor/state-management#in-memory-state-container-service> .</span><span class="sxs-lookup"><span data-stu-id="cba72-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see <xref:blazor/state-management#in-memory-state-container-service>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cba72-189">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cba72-189">Additional resources</span></span>

* [<span data-ttu-id="cba72-190">Powiązywanie z przyciskami radiowymi w formularzu</span><span class="sxs-lookup"><span data-stu-id="cba72-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="cba72-191">Powiązywanie `<select>` opcji elementu z wartościami obiektów C# `null` w formularzu</span><span class="sxs-lookup"><span data-stu-id="cba72-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
