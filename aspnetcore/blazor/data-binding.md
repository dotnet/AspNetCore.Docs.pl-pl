---
<span data-ttu-id="a4a45-101">title: "ASP.NET Core Blazor powiązania danych" Author: Description: "informacje o funkcjach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="a4a45-101">title: 'ASP.NET Core Blazor data binding' author: description: 'Learn about data binding features for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="a4a45-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a4a45-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a4a45-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a4a45-103">'Blazor'</span></span>
- <span data-ttu-id="a4a45-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a4a45-104">'Identity'</span></span>
- <span data-ttu-id="a4a45-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a4a45-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="a4a45-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a4a45-106">'Razor'</span></span>
- <span data-ttu-id="a4a45-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a4a45-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="a4a45-108">ASP.NET Core Blazor powiązania danych</span><span class="sxs-lookup"><span data-stu-id="a4a45-108">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="a4a45-109">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a4a45-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="a4a45-110">składniki zapewniają funkcje powiązań danych za pośrednictwem atrybutu elementu HTML o nazwie [`@bind`](xref:mvc/views/razor#bind) z wartością pola, właściwości lub Razor wyrażenia.</span><span class="sxs-lookup"><span data-stu-id="a4a45-110"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="a4a45-111">Poniższy przykład wiąże `CurrentValue` Właściwość z wartością pola tekstowego:</span><span class="sxs-lookup"><span data-stu-id="a4a45-111">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="a4a45-112">Gdy pole tekstowe utraci fokus, wartość właściwości jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="a4a45-112">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="a4a45-113">Pole tekstowe jest aktualizowane w interfejsie użytkownika tylko wtedy, gdy składnik jest renderowany, a nie w odpowiedzi na zmianę wartości właściwości.</span><span class="sxs-lookup"><span data-stu-id="a4a45-113">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="a4a45-114">Ponieważ składniki renderują się po wykonaniu kodu procedury obsługi zdarzeń, aktualizacje właściwości są *zwykle* odzwierciedlane w interfejsie użytkownika natychmiast po wyzwoleniu programu obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a4a45-114">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="a4a45-115">Używanie [`@bind`](xref:mvc/views/razor#bind) z `CurrentValue` właściwością ( `<input @bind="CurrentValue" />` ) jest zasadniczo równoważne z następującymi:</span><span class="sxs-lookup"><span data-stu-id="a4a45-115">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="a4a45-116">Gdy składnik jest renderowany, `value` element wejściowy pochodzi z `CurrentValue` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a4a45-116">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="a4a45-117">Gdy użytkownik wpisze w polu tekstowym i zmieni fokus elementu, `onchange` zdarzenie jest wywoływane i `CurrentValue` Właściwość jest ustawiana na wartość zmieniona.</span><span class="sxs-lookup"><span data-stu-id="a4a45-117">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="a4a45-118">W rzeczywistości generowanie kodu jest bardziej skomplikowane, ponieważ [`@bind`](xref:mvc/views/razor#bind) obsługuje przypadki, w których są wykonywane konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="a4a45-118">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="a4a45-119">W zasadzie [`@bind`](xref:mvc/views/razor#bind) kojarzy bieżącą wartość wyrażenia z `value` atrybutem i obsługuje zmiany przy użyciu zarejestrowanej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="a4a45-119">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="a4a45-120">Powiąż właściwość lub pole w innych zdarzeniach, dołączając także `@bind:event` atrybut z `event` parametrem.</span><span class="sxs-lookup"><span data-stu-id="a4a45-120">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="a4a45-121">Poniższy przykład wiąże `CurrentValue` Właściwość `oninput` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="a4a45-121">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="a4a45-122">W przeciwieństwie do `onchange` , które jest wyzwalane, gdy element utraci fokus, `oninput` jest uruchamiany po zmianie wartości pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="a4a45-122">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="a4a45-123">Użyj `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` składni with, aby powiązać atrybuty elementu inne niż `value` .</span><span class="sxs-lookup"><span data-stu-id="a4a45-123">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="a4a45-124">W poniższym przykładzie styl akapitu zostanie zaktualizowany po `paragraphStyle` zmianie wartości:</span><span class="sxs-lookup"><span data-stu-id="a4a45-124">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

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

<span data-ttu-id="a4a45-125">W powiązaniu atrybutu rozróżniana jest wielkość liter:</span><span class="sxs-lookup"><span data-stu-id="a4a45-125">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="a4a45-126">`@bind`jest prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="a4a45-126">`@bind` is valid.</span></span>
* <span data-ttu-id="a4a45-127">`@Bind`i `@BIND` są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="a4a45-127">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="a4a45-128">Wartości niemożliwy do przeanalizowania</span><span class="sxs-lookup"><span data-stu-id="a4a45-128">Unparsable values</span></span>

<span data-ttu-id="a4a45-129">Gdy użytkownik dostarczy wartość niemożliwy do przeanalizowania do elementu powiązanego z danymi, wartość niemożliwy do przeanalizowania jest automatycznie przywracana do poprzedniej wartości po wyzwoleniu zdarzenia bind.</span><span class="sxs-lookup"><span data-stu-id="a4a45-129">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="a4a45-130">Poniżej przedstawiono przykładowy scenariusz:</span><span class="sxs-lookup"><span data-stu-id="a4a45-130">Consider the following scenario:</span></span>

* <span data-ttu-id="a4a45-131">`<input>`Element jest powiązany z `int` typem z początkową wartością `123` :</span><span class="sxs-lookup"><span data-stu-id="a4a45-131">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="a4a45-132">Użytkownik aktualizuje wartość elementu na `123.45` liście na stronie i zmienia fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="a4a45-132">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="a4a45-133">W poprzednim scenariuszu wartość elementu jest przywracana `123` .</span><span class="sxs-lookup"><span data-stu-id="a4a45-133">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="a4a45-134">Gdy wartość `123.45` zostanie odrzucona na korzyść oryginalnej wartości `123` , użytkownik rozumie, że ich wartość nie została zaakceptowana.</span><span class="sxs-lookup"><span data-stu-id="a4a45-134">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="a4a45-135">Domyślnie powiązanie dotyczy `onchange` zdarzenia elementu ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="a4a45-135">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="a4a45-136">Służy `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` do wyzwalania powiązań na innym zdarzeniu.</span><span class="sxs-lookup"><span data-stu-id="a4a45-136">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="a4a45-137">W przypadku `oninput` zdarzenia ( `@bind:event="oninput"` ) jego wersja następuje po naciśnięciu klawisza, które wprowadza niemożliwy do przeanalizowania wartość.</span><span class="sxs-lookup"><span data-stu-id="a4a45-137">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="a4a45-138">Podczas określania wartości docelowej dla `oninput` zdarzenia z `int` typem związanym użytkownik nie jest w trakcie wpisywania `.` znaku.</span><span class="sxs-lookup"><span data-stu-id="a4a45-138">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="a4a45-139">`.`Znak zostanie natychmiast usunięty, więc użytkownik otrzymuje natychmiastową opinię, że dozwolone są tylko liczby całkowite.</span><span class="sxs-lookup"><span data-stu-id="a4a45-139">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="a4a45-140">Istnieją scenariusze, w których przywrócenie wartości `oninput` zdarzenia nie jest idealne, na przykład wtedy, gdy użytkownik powinien mieć możliwość wyczyszczenia wartości, która nie może być przewidziana `<input>` .</span><span class="sxs-lookup"><span data-stu-id="a4a45-140">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="a4a45-141">Alternatywy obejmują:</span><span class="sxs-lookup"><span data-stu-id="a4a45-141">Alternatives include:</span></span>

* <span data-ttu-id="a4a45-142">Nie używaj `oninput` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="a4a45-142">Don't use the `oninput` event.</span></span> <span data-ttu-id="a4a45-143">Użyj zdarzenia domyślnego `onchange` (tylko określenie `@bind="{PROPERTY OR FIELD}"` ), gdzie nie jest przywracana nieprawidłowa wartość, dopóki element nie utraci fokusu.</span><span class="sxs-lookup"><span data-stu-id="a4a45-143">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="a4a45-144">Powiąż z typem dopuszczającym wartość null, taką jak `int?` lub `string` , i podaj logikę niestandardową do obsługi nieprawidłowych wpisów.</span><span class="sxs-lookup"><span data-stu-id="a4a45-144">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="a4a45-145">Użyj [składnika walidacji formularza](xref:blazor/forms-validation), takiego jak <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> lub <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="a4a45-145">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="a4a45-146">Składniki walidacji formularza mają wbudowaną obsługę zarządzania nieprawidłowymi danymi wejściowymi.</span><span class="sxs-lookup"><span data-stu-id="a4a45-146">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="a4a45-147">Składniki walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="a4a45-147">Form validation components:</span></span>
  * <span data-ttu-id="a4a45-148">Zezwalaj użytkownikowi na dostarczenie nieprawidłowych danych wejściowych i odbieranie błędów walidacji w skojarzonym <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="a4a45-148">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="a4a45-149">Wyświetlaj błędy walidacji w interfejsie użytkownika bez zakłócania wprowadzania dodatkowych danych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a4a45-149">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="a4a45-150">Ciągi formatujące</span><span class="sxs-lookup"><span data-stu-id="a4a45-150">Format strings</span></span>

<span data-ttu-id="a4a45-151">Powiązanie danych działa z <xref:System.DateTime> ciągami formatu przy użyciu `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="a4a45-151">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="a4a45-152">W tej chwili nie są dostępne inne wyrażenia formatu, takie jak formaty walutowe lub liczbowe.</span><span class="sxs-lookup"><span data-stu-id="a4a45-152">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="a4a45-153">W poprzednim kodzie `<input>` Typ pola () elementu jest `type` wartością domyślną `text` .</span><span class="sxs-lookup"><span data-stu-id="a4a45-153">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="a4a45-154">`@bind:format`jest obsługiwana w celu powiązania następujących typów .NET:</span><span class="sxs-lookup"><span data-stu-id="a4a45-154">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="a4a45-155"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="a4a45-155"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="a4a45-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="a4a45-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="a4a45-157">Ten `@bind:format` atrybut określa format daty, który ma zostać zastosowany do `value` `<input>` elementu.</span><span class="sxs-lookup"><span data-stu-id="a4a45-157">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="a4a45-158">Format jest również używany do analizowania wartości w przypadku `onchange` wystąpienia zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="a4a45-158">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="a4a45-159">Określanie formatu dla `date` typu pola nie jest zalecane, ponieważ Blazor ma wbudowaną obsługę formatowania dat.</span><span class="sxs-lookup"><span data-stu-id="a4a45-159">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="a4a45-160">Pomimo zalecenia, należy używać tylko `yyyy-MM-dd` formatu daty do poprawnego działania, jeśli format jest dostarczany z `date` typem pola:</span><span class="sxs-lookup"><span data-stu-id="a4a45-160">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="a4a45-161">Powiązanie element nadrzędny-to-Child z parametrami składnika</span><span class="sxs-lookup"><span data-stu-id="a4a45-161">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="a4a45-162">Powiązanie rozpoznaje parametry składnika, gdzie `@bind-{PROPERTY}` można powiązać wartość właściwości z składnika nadrzędnego w dół ze składnikiem podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="a4a45-162">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="a4a45-163">Powiązanie z elementu podrzędnego z elementem nadrzędnym jest omówione w [powiązaniu podrzędnie-to-Parent z częściowym powiązaniem powiązania](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="a4a45-163">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="a4a45-164">Następujący składnik podrzędny ( `ChildComponent` ) ma `Year` parametr składnika i `YearChanged` wywołanie zwrotne:</span><span class="sxs-lookup"><span data-stu-id="a4a45-164">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="a4a45-165"><xref:Microsoft.AspNetCore.Components.EventCallback%601>wyjaśniono w temacie <xref:blazor/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="a4a45-165"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="a4a45-166">Poniższy składnik nadrzędny używa:</span><span class="sxs-lookup"><span data-stu-id="a4a45-166">The following parent component uses:</span></span>

* <span data-ttu-id="a4a45-167">`ChildComponent`i wiąże `ParentYear` parametr z elementu nadrzędnego z `Year` parametrem w składniku podrzędnym.</span><span class="sxs-lookup"><span data-stu-id="a4a45-167">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="a4a45-168">To `onclick` zdarzenie służy do wyzwalania `ChangeTheYear` metody.</span><span class="sxs-lookup"><span data-stu-id="a4a45-168">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="a4a45-169">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="a4a45-169">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="a4a45-170">Załadowanie `ParentComponent` powoduje utworzenie następującej adjustacji:</span><span class="sxs-lookup"><span data-stu-id="a4a45-170">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="a4a45-171">Jeśli wartość `ParentYear` właściwości zostanie zmieniona przez wybranie przycisku w `ParentComponent` , `Year` Właściwość `ChildComponent` jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="a4a45-171">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="a4a45-172">Nowa wartość `Year` jest renderowana w interfejsie użytkownika podczas jego `ParentComponent` renderowania:</span><span class="sxs-lookup"><span data-stu-id="a4a45-172">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="a4a45-173">`Year`Parametr jest możliwy do powiązania, ponieważ ma zdarzenie towarzyszące `YearChanged` pasujące do typu `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="a4a45-173">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="a4a45-174">Zgodnie z Konwencją, `<ChildComponent @bind-Year="ParentYear" />` jest zasadniczo równoważne zapisowi:</span><span class="sxs-lookup"><span data-stu-id="a4a45-174">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="a4a45-175">Ogólnie rzecz biorąc, właściwość może być powiązana z odpowiadającą jej obsługą zdarzeń przez uwzględnienie `@bind-{PROPRETY}:event` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a4a45-175">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="a4a45-176">Na przykład właściwość `MyProp` może być powiązana z `MyEventHandler` użyciem następujących dwóch atrybutów:</span><span class="sxs-lookup"><span data-stu-id="a4a45-176">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="a4a45-177">Powiązanie elementu podrzędnego z elementem nadrzędnym z powiązaniem łańcuchowym</span><span class="sxs-lookup"><span data-stu-id="a4a45-177">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="a4a45-178">Typowy scenariusz polega na łańcuchu parametru powiązanego z danymi do elementu strony w danych wyjściowych składnika.</span><span class="sxs-lookup"><span data-stu-id="a4a45-178">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="a4a45-179">Ten scenariusz jest nazywany *powiązaniem łańcuchowym* , ponieważ wiele poziomów powiązań występuje jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="a4a45-179">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="a4a45-180">Nie można zaimplementować powiązania łańcuchowego przy użyciu [`@bind`](xref:mvc/views/razor#bind) składni w elemencie strony.</span><span class="sxs-lookup"><span data-stu-id="a4a45-180">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="a4a45-181">Program obsługi zdarzeń i wartość muszą być określone osobno.</span><span class="sxs-lookup"><span data-stu-id="a4a45-181">The event handler and value must be specified separately.</span></span> <span data-ttu-id="a4a45-182">Składnik nadrzędny, jednak może używać [`@bind`](xref:mvc/views/razor#bind) składni z parametrem składnika.</span><span class="sxs-lookup"><span data-stu-id="a4a45-182">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="a4a45-183">Następujący `PasswordField` składnik (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="a4a45-183">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="a4a45-184">Ustawia `<input>` wartość elementu na `Password` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="a4a45-184">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="a4a45-185">Uwidacznia zmiany `Password` właściwości w składniku nadrzędnym z [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="a4a45-185">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="a4a45-186">Używa `onclick` zdarzenia, aby wyzwolić `ToggleShowPassword` metodę.</span><span class="sxs-lookup"><span data-stu-id="a4a45-186">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="a4a45-187">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="a4a45-187">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="a4a45-188">`PasswordField`Składnik jest używany w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="a4a45-188">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="a4a45-189">Aby przeprowadzić sprawdzenia lub błędy pułapki dla hasła w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a4a45-189">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="a4a45-190">Utwórz pole zapasowe dla `Password` ( `password` w poniższym przykładowym kodzie).</span><span class="sxs-lookup"><span data-stu-id="a4a45-190">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="a4a45-191">Wykonaj testy lub błędy pułapek w metodzie `Password` ustawiającej.</span><span class="sxs-lookup"><span data-stu-id="a4a45-191">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="a4a45-192">Poniższy przykład przedstawia natychmiastową opinię dla użytkownika, jeśli w wartości hasła jest używana spacja:</span><span class="sxs-lookup"><span data-stu-id="a4a45-192">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="a4a45-193">Przyciski radiowe</span><span class="sxs-lookup"><span data-stu-id="a4a45-193">Radio buttons</span></span>

<span data-ttu-id="a4a45-194">Aby uzyskać informacje na temat tworzenia powiązań z przyciskami radiowymi w formularzu, zobacz <xref:blazor/forms-validation#work-with-radio-buttons> .</span><span class="sxs-lookup"><span data-stu-id="a4a45-194">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
