---
title: ASP.NET Core Blazor formularzy i walidacji
author: guardrex
description: Dowiedz się, jak używać scenariuszy i walidacji pól w programie Blazor .
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
uid: blazor/forms-validation
ms.openlocfilehash: 707686cdc4ceb6605d6214eeeee0f6dd2e121c90
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679530"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="77e51-103">ASP.NET Core Blazor formularzy i walidacji</span><span class="sxs-lookup"><span data-stu-id="77e51-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="77e51-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="77e51-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="77e51-105">Formularze i walidacje są obsługiwane w przypadku Blazor używania [adnotacji danych](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="77e51-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="77e51-106">Następujący `ExampleModel` Typ definiuje logikę walidacji przy użyciu adnotacji danych:</span><span class="sxs-lookup"><span data-stu-id="77e51-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="77e51-107">Formularz jest definiowany przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składnika.</span><span class="sxs-lookup"><span data-stu-id="77e51-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="77e51-108">W poniższej formie przedstawiono typowe elementy, składniki i Razor kod:</span><span class="sxs-lookup"><span data-stu-id="77e51-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="77e51-109">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="77e51-109">In the preceding example:</span></span>

* <span data-ttu-id="77e51-110">Formularz sprawdza poprawność danych wejściowych użytkownika w `name` polu przy użyciu walidacji zdefiniowanej w `ExampleModel` typie.</span><span class="sxs-lookup"><span data-stu-id="77e51-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="77e51-111">Model jest tworzony w `@code` bloku składnika i przechowywany w prywatnym polu ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="77e51-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="77e51-112">Pole jest przypisane do `Model` atrybutu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="77e51-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="77e51-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Powiązania składnika:</span><span class="sxs-lookup"><span data-stu-id="77e51-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="77e51-114">Właściwość modelu ( `exampleModel.Name` ) do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="77e51-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="77e51-115">Aby uzyskać więcej informacji na temat powiązania właściwości, zobacz <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="77e51-115">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="77e51-116">Delegowanie zdarzenia zmiany do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="77e51-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="77e51-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Składnik dołącza obsługę walidacji przy użyciu adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="77e51-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="77e51-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje komunikaty weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="77e51-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="77e51-119">`HandleValidSubmit`jest wyzwalany po pomyślnym przesłaniu formularza (kończy walidację).</span><span class="sxs-lookup"><span data-stu-id="77e51-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="77e51-120">Zestaw wbudowanych składników wejściowych jest dostępny do odbierania i weryfikowania danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="77e51-120">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="77e51-121">Dane wejściowe są weryfikowane po ich zmianie i po przesłaniu formularza.</span><span class="sxs-lookup"><span data-stu-id="77e51-121">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="77e51-122">W poniższej tabeli przedstawiono dostępne składniki danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="77e51-122">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="77e51-123">Składnik wejściowy</span><span class="sxs-lookup"><span data-stu-id="77e51-123">Input component</span></span> | <span data-ttu-id="77e51-124">Renderowane jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="77e51-124">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<span data-ttu-id="77e51-125">Wszystkie składniki danych wejściowych, w tym <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="77e51-125">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="77e51-126">Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do renderowanego elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="77e51-126">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="77e51-127">Składniki wejściowe zapewniają domyślne zachowanie podczas sprawdzania poprawności edycji i zmiany ich klasy CSS, aby odzwierciedlały stan pola.</span><span class="sxs-lookup"><span data-stu-id="77e51-127">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="77e51-128">Niektóre składniki obejmują przydatne logiki analizy.</span><span class="sxs-lookup"><span data-stu-id="77e51-128">Some components include useful parsing logic.</span></span> <span data-ttu-id="77e51-129">Na przykład i bezproblemowo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> obsłużyć wartości, które można przeanalizować, rejestrując je jako błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="77e51-129">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="77e51-130">Typy, które mogą akceptować wartości null, obsługują również wartość null pola docelowego (na przykład `int?` ).</span><span class="sxs-lookup"><span data-stu-id="77e51-130">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="77e51-131">Następujący `Starship` Typ definiuje logikę walidacji przy użyciu większego zestawu właściwości i adnotacji danych niż wcześniej `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="77e51-131">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="77e51-132">W powyższym przykładzie `Description` jest opcjonalne, ponieważ nie są obecne adnotacje danych.</span><span class="sxs-lookup"><span data-stu-id="77e51-132">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="77e51-133">Następujący formularz sprawdza poprawność danych wejściowych użytkownika przy użyciu weryfikacji zdefiniowanej w `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="77e51-133">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="77e51-134"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Tworzy <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [wartość kaskadową](xref:blazor/components#cascading-values-and-parameters) , która śledzi metadane dotyczące procesu edycji, w tym pola, które zostały zmodyfikowane i bieżące komunikaty weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="77e51-134">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="77e51-135"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Zapewnia również wygodne zdarzenia dla prawidłowych i nieprawidłowych przesyłania ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ).</span><span class="sxs-lookup"><span data-stu-id="77e51-135">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="77e51-136">Alternatywnie można użyć <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> do wyzwolenia walidacji i sprawdzenia wartości pól z niestandardowym kodem walidacji.</span><span class="sxs-lookup"><span data-stu-id="77e51-136">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="77e51-137">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="77e51-137">In the following example:</span></span>

* <span data-ttu-id="77e51-138">`HandleSubmit`Metoda jest uruchamiana po wybraniu przycisku **Prześlij** .</span><span class="sxs-lookup"><span data-stu-id="77e51-138">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="77e51-139">Formularz zostanie sprawdzony przy użyciu formularza <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="77e51-139">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="77e51-140">Formularz jest weryfikowany przez przekazanie <xref:Microsoft.AspNetCore.Components.Forms.EditContext> do `ServerValidate` metody, która wywołuje punkt końcowy interfejsu API sieci Web na serwerze (*niepokazywany*).</span><span class="sxs-lookup"><span data-stu-id="77e51-140">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="77e51-141">Dodatkowy kod jest uruchamiany w zależności od wyniku sprawdzenia poprawności po stronie klienta i serwera `isValid` .</span><span class="sxs-lookup"><span data-stu-id="77e51-141">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="77e51-142">InputText na podstawie zdarzenia wejściowego</span><span class="sxs-lookup"><span data-stu-id="77e51-142">InputText based on the input event</span></span>

<span data-ttu-id="77e51-143">Użyj <xref:Microsoft.AspNetCore.Components.Forms.InputText> składnika, aby utworzyć niestandardowy składnik, który używa `input` zdarzenia zamiast `change` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="77e51-143">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="77e51-144">W poniższym przykładzie `CustomInputText` składnik dziedziczy `InputText` składnik platformy i ustawia powiązanie zdarzenia ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) na `oninput` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="77e51-144">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="77e51-145">*Shared/CustomInputText. Razor*:</span><span class="sxs-lookup"><span data-stu-id="77e51-145">*Shared/CustomInputText.razor*:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="77e51-146">`CustomInputText`Składnika można używać wszędzie tam, gdzie <xref:Microsoft.AspNetCore.Components.Forms.InputText> są używane:</span><span class="sxs-lookup"><span data-stu-id="77e51-146">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="77e51-147">*Strony/TestForm. Razor*:</span><span class="sxs-lookup"><span data-stu-id="77e51-147">*Pages/TestForm.razor*:</span></span>

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="77e51-148">Pracuj z przyciskami radiowymi</span><span class="sxs-lookup"><span data-stu-id="77e51-148">Work with radio buttons</span></span>

<span data-ttu-id="77e51-149">Podczas pracy z przyciskami radiowymi w formularzu powiązanie danych jest obsługiwane inaczej niż inne elementy, ponieważ przyciski radiowe są oceniane jako Grupa.</span><span class="sxs-lookup"><span data-stu-id="77e51-149">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="77e51-150">Wartość każdego przycisku radiowego jest stała, ale wartość grupy przycisków radiowych jest wartością wybranego przycisku radiowego.</span><span class="sxs-lookup"><span data-stu-id="77e51-150">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="77e51-151">Poniższy przykład pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="77e51-151">The following example shows how to:</span></span>

* <span data-ttu-id="77e51-152">Obsługa powiązania danych dla grupy przycisków radiowych.</span><span class="sxs-lookup"><span data-stu-id="77e51-152">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="77e51-153">Obsługa walidacji przy użyciu `InputRadio` składnika niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="77e51-153">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="77e51-154">Poniższe <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składniki używają powyższego `InputRadio` składnika do uzyskania i zweryfikowania klasyfikacji od użytkownika:</span><span class="sxs-lookup"><span data-stu-id="77e51-154">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a><span data-ttu-id="77e51-155">Obsługa walidacji</span><span class="sxs-lookup"><span data-stu-id="77e51-155">Validation support</span></span>

<span data-ttu-id="77e51-156"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Składnik dołącza obsługę walidacji przy użyciu adnotacji danych do kaskadowo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="77e51-156">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="77e51-157">Włączenie obsługi walidacji przy użyciu adnotacji danych wymaga tego jawnego gestu.</span><span class="sxs-lookup"><span data-stu-id="77e51-157">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="77e51-158">Aby użyć innego systemu sprawdzania poprawności niż adnotacje danych, Zastąp zmienną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> implementacją niestandardową.</span><span class="sxs-lookup"><span data-stu-id="77e51-158">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="77e51-159">Implementacja ASP.NET Core jest dostępna do inspekcji w źródle referencyjnym: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="77e51-159">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="77e51-160">wykonuje dwa typy walidacji:</span><span class="sxs-lookup"><span data-stu-id="77e51-160"> performs two types of validation:</span></span>

* <span data-ttu-id="77e51-161">*Sprawdzanie poprawności pola* jest wykonywane, gdy użytkownik wyprowadzi karty z pola.</span><span class="sxs-lookup"><span data-stu-id="77e51-161">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="77e51-162">Podczas sprawdzania poprawności pola <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik kojarzy wszystkie zgłoszone wyniki walidacji z polem.</span><span class="sxs-lookup"><span data-stu-id="77e51-162">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="77e51-163">*Sprawdzanie poprawności modelu* jest wykonywane, gdy użytkownik prześle formularz.</span><span class="sxs-lookup"><span data-stu-id="77e51-163">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="77e51-164">Podczas walidacji modelu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik próbuje określić pole na podstawie nazwy elementu członkowskiego, który raportuje wynik sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="77e51-164">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="77e51-165">Wyniki walidacji, które nie są skojarzone z poszczególnymi elementami członkowskimi, są skojarzone z modelem, a nie polem.</span><span class="sxs-lookup"><span data-stu-id="77e51-165">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="77e51-166">Składniki podsumowania walidacji i komunikatów weryfikacji</span><span class="sxs-lookup"><span data-stu-id="77e51-166">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="77e51-167"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje wszystkie komunikaty weryfikacyjne podobne do [pomocnika tagów podsumowania weryfikacji](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="77e51-167">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="77e51-168">Wyprowadź komunikaty weryfikacji dla określonego modelu z `Model` parametrem:</span><span class="sxs-lookup"><span data-stu-id="77e51-168">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="77e51-169"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składnik wyświetla komunikaty sprawdzania poprawności dla określonego pola, które jest podobne do [pomocnika tagów komunikatu weryfikacji](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="77e51-169">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="77e51-170">Określ pole do walidacji z `For` atrybutem i wyrażeniem lambda, które nazywa właściwość modelu:</span><span class="sxs-lookup"><span data-stu-id="77e51-170">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="77e51-171"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składniki i <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="77e51-171">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="77e51-172">Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do wygenerowanego `<div>` `<ul>` elementu or.</span><span class="sxs-lookup"><span data-stu-id="77e51-172">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="77e51-173">Niestandardowe atrybuty walidacji</span><span class="sxs-lookup"><span data-stu-id="77e51-173">Custom validation attributes</span></span>

<span data-ttu-id="77e51-174">Aby upewnić się, że wynik walidacji jest prawidłowo skojarzony z polem przy użyciu [niestandardowego atrybutu walidacji](xref:mvc/models/validation#custom-attributes), należy przekazać kontekst walidacji <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> podczas tworzenia <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="77e51-174">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="77e51-175">Pakiet weryfikacji adnotacji danych</span><span class="sxs-lookup"><span data-stu-id="77e51-175"> data annotations validation package</span></span>

<span data-ttu-id="77e51-176">[Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) to pakiet, który wypełnia luki w środowisku walidacji przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="77e51-176">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="77e51-177">Pakiet jest obecnie *eksperymentalny*.</span><span class="sxs-lookup"><span data-stu-id="77e51-177">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="77e51-178">[CompareProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="77e51-178">[CompareProperty] attribute</span></span>

<span data-ttu-id="77e51-179"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nie działa dobrze ze <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnikiem, ponieważ nie kojarzy wyniku walidacji z określonym elementem członkowskim.</span><span class="sxs-lookup"><span data-stu-id="77e51-179">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="77e51-180">Może to spowodować niespójne zachowanie między walidacją na poziomie pola i po sprawdzeniu poprawności całego modelu podczas przesyłania.</span><span class="sxs-lookup"><span data-stu-id="77e51-180">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="77e51-181">Pakiet *eksperymentalny* [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) wprowadza dodatkowy atrybut sprawdzania poprawności, `ComparePropertyAttribute` który działa wokół tych ograniczeń.</span><span class="sxs-lookup"><span data-stu-id="77e51-181">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="77e51-182">W Blazor aplikacji, `[CompareProperty]` jest bezpośrednią wymianą dla [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="77e51-182">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="77e51-183">Modele zagnieżdżone, typy kolekcji i typy złożone</span><span class="sxs-lookup"><span data-stu-id="77e51-183">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="77e51-184">zapewnia obsługę sprawdzania poprawności formularza przy użyciu adnotacji danych z wbudowaną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="77e51-184"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="77e51-185">Jednak program <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> sprawdza poprawność właściwości najwyższego poziomu modelu powiązanego z formularzem, który nie jest właściwościami typu Collection-lub złożonego.</span><span class="sxs-lookup"><span data-stu-id="77e51-185">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="77e51-186">Aby sprawdzić poprawność całego grafu obiektów modelu powiązanego, w tym właściwości kolekcji i typu złożonego, użyj `ObjectGraphDataAnnotationsValidator` dostarczonej przez *eksperymentalny* pakiet [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="77e51-186">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="77e51-187">Dodawanie adnotacji do właściwości modelu za pomocą `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="77e51-187">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="77e51-188">W poniższych klasach modelu `ShipDescription` Klasa zawiera dodatkowe adnotacje danych do zweryfikowania, gdy model jest powiązany z formularzem:</span><span class="sxs-lookup"><span data-stu-id="77e51-188">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="77e51-189">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="77e51-189">*Starship.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="77e51-190">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="77e51-190">*ShipDescription.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="77e51-191">Włączanie przycisku przesyłania na podstawie walidacji formularza</span><span class="sxs-lookup"><span data-stu-id="77e51-191">Enable the submit button based on form validation</span></span>

<span data-ttu-id="77e51-192">Aby włączyć i wyłączyć przycisk Prześlij na podstawie walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="77e51-192">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="77e51-193">Użyj formularza, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Aby przypisać model, gdy składnik zostanie zainicjowany.</span><span class="sxs-lookup"><span data-stu-id="77e51-193">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="77e51-194">Sprawdź poprawność formularza w <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> wywołaniu zwrotnym kontekstu, aby włączyć i wyłączyć przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="77e51-194">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="77e51-195">Odpinanie programu obsługi zdarzeń w `Dispose` metodzie.</span><span class="sxs-lookup"><span data-stu-id="77e51-195">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="77e51-196">Aby uzyskać więcej informacji, zobacz <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="77e51-196">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="77e51-197">W poprzednim przykładzie ustaw wartość `formInvalid` `false` :</span><span class="sxs-lookup"><span data-stu-id="77e51-197">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="77e51-198">Formularz jest wstępnie załadowany z prawidłowymi wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="77e51-198">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="77e51-199">Przycisk Prześlij ma być włączony podczas ładowania formularza.</span><span class="sxs-lookup"><span data-stu-id="77e51-199">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="77e51-200">Efektem ubocznym poprzedniego podejścia jest to, że <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jest wypełniany przy użyciu nieprawidłowych pól, gdy użytkownik przeprowadzi interakcję z jednym polem.</span><span class="sxs-lookup"><span data-stu-id="77e51-200">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="77e51-201">Ten scenariusz można rozwiązać w jeden z następujących sposobów:</span><span class="sxs-lookup"><span data-stu-id="77e51-201">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="77e51-202">Nie używaj <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnika w formularzu.</span><span class="sxs-lookup"><span data-stu-id="77e51-202">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="77e51-203">Ustaw <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jako widoczny po wybraniu przycisku Prześlij (na przykład w `HandleValidSubmit` metodzie).</span><span class="sxs-lookup"><span data-stu-id="77e51-203">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="77e51-204">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="77e51-204">Troubleshoot</span></span>

> <span data-ttu-id="77e51-205">InvalidOperationException: EditForm wymaga parametru modelu lub parametru EditContext, ale nie obu.</span><span class="sxs-lookup"><span data-stu-id="77e51-205">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="77e51-206">Upewnij się, że <xref:Microsoft.AspNetCore.Components.Forms.EditForm> element ma wartość <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> lub <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="77e51-206">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="77e51-207">Podczas przypisywania <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do formularza upewnij się, że typ modelu jest skonkretyzowany, jak pokazano na poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="77e51-207">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
