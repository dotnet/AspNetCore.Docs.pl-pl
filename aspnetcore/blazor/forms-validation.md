---
title: ASP.NET Podstawowe Blazor formularze i walidacja
author: guardrex
description: Dowiedz się, jak używać formularzy Blazori scenariuszy sprawdzania poprawności pól w pliku .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218963"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="924e0-103">ASP.NET podstawowe formularze Blazora i walidacja</span><span class="sxs-lookup"><span data-stu-id="924e0-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="924e0-104">Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="924e0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="924e0-105">Formularze i sprawdzanie poprawności są obsługiwane w blazorze przy użyciu [adnotacji danych](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="924e0-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="924e0-106">Następujący `ExampleModel` typ definiuje logikę sprawdzania poprawności przy użyciu adnotacji danych:</span><span class="sxs-lookup"><span data-stu-id="924e0-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="924e0-107">Formularz jest definiowany `EditForm` za pomocą komponentu.</span><span class="sxs-lookup"><span data-stu-id="924e0-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="924e0-108">Poniższy formularz pokazuje typowe elementy, składniki i kod Razor:</span><span class="sxs-lookup"><span data-stu-id="924e0-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="924e0-109">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="924e0-109">In the preceding example:</span></span>

* <span data-ttu-id="924e0-110">Formularz sprawdza poprawność danych `name` wejściowych użytkownika w `ExampleModel` polu przy użyciu sprawdzania poprawności zdefiniowanej w typie.</span><span class="sxs-lookup"><span data-stu-id="924e0-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="924e0-111">Model jest tworzony w `@code` bloku komponentu i utrzymywany`_exampleModel`w prywatnym polu ( ).</span><span class="sxs-lookup"><span data-stu-id="924e0-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="924e0-112">Pole jest przypisane `Model` do atrybutu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="924e0-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="924e0-113">`@bind-Value` Wiąże `InputText` składnik:</span><span class="sxs-lookup"><span data-stu-id="924e0-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="924e0-114">Właściwość modelu`_exampleModel.Name`( `InputText` ) do `Value` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="924e0-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="924e0-115">Delegat zdarzenia zmiany `InputText` do właściwości `ValueChanged` składnika.</span><span class="sxs-lookup"><span data-stu-id="924e0-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="924e0-116">Składnik `DataAnnotationsValidator` dołącza obsługę sprawdzania poprawności przy użyciu adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="924e0-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="924e0-117">Składnik `ValidationSummary` podsumowuje komunikaty sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="924e0-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="924e0-118">`HandleValidSubmit`jest wyzwalany, gdy formularz zostanie pomyślnie przesłany (przechodzi weryfikację).</span><span class="sxs-lookup"><span data-stu-id="924e0-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="924e0-119">Zestaw wbudowanych składników wejściowych są dostępne do odbierania i sprawdzania poprawności danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="924e0-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="924e0-120">Dane wejściowe są sprawdzane po ich zmianie i przesłaniu formularza.</span><span class="sxs-lookup"><span data-stu-id="924e0-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="924e0-121">Dostępne składniki wejściowe przedstawiono w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="924e0-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="924e0-122">Składnik wejściowy</span><span class="sxs-lookup"><span data-stu-id="924e0-122">Input component</span></span> | <span data-ttu-id="924e0-123">Renderowane jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="924e0-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="924e0-124">Wszystkie składniki wejściowe, `EditForm`w tym , obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="924e0-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="924e0-125">Każdy atrybut, który nie pasuje do parametru składnika, jest dodawany do renderowanego elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="924e0-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="924e0-126">Składniki wejściowe zapewniają domyślne zachowanie sprawdzania poprawności podczas edytowania i zmieniania klasy CSS w celu odzwierciedlenia stanu pola.</span><span class="sxs-lookup"><span data-stu-id="924e0-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="924e0-127">Niektóre składniki zawierają logikę analizowania przydatne.</span><span class="sxs-lookup"><span data-stu-id="924e0-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="924e0-128">Na przykład `InputDate` `InputNumber` i obsługiwać wartości niepardzielalne bezpiecznie rejestrując je jako błędy sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="924e0-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="924e0-129">Typy, które mogą akceptować wartości null, obsługują `int?`również nieważność pola docelowego (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="924e0-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="924e0-130">Następujący `Starship` typ definiuje logikę sprawdzania poprawności przy użyciu większego zestawu właściwości `ExampleModel`i adnotacji danych niż wcześniej:</span><span class="sxs-lookup"><span data-stu-id="924e0-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="924e0-131">W poprzednim przykładzie `Description` jest opcjonalne, ponieważ nie są obecne adnotacje danych.</span><span class="sxs-lookup"><span data-stu-id="924e0-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="924e0-132">Następujący formularz sprawdza poprawność danych wejściowych `Starship` użytkownika przy użyciu sprawdzania poprawności zdefiniowanej w modelu:</span><span class="sxs-lookup"><span data-stu-id="924e0-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
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
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
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
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="924e0-133">Tworzy `EditForm` `EditContext` jako [wartość kaskadową,](xref:blazor/components#cascading-values-and-parameters) która śledzi metadane o procesie edycji, w tym, które pola zostały zmodyfikowane i bieżące komunikaty sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="924e0-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="924e0-134">Zapewnia `EditForm` również wygodne zdarzenia dla prawidłowych`OnValidSubmit` `OnInvalidSubmit`i nieprawidłowych przesłań ( , ).</span><span class="sxs-lookup"><span data-stu-id="924e0-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="924e0-135">Alternatywnie można `OnSubmit` użyć do wyzwolenia sprawdzania poprawności i sprawdzić wartości pól za pomocą niestandardowego kodu sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="924e0-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="924e0-136">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="924e0-136">In the following example:</span></span>

* <span data-ttu-id="924e0-137">Metoda `HandleSubmit` jest uruchamiana po wybraniu przycisku **Prześlij.**</span><span class="sxs-lookup"><span data-stu-id="924e0-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="924e0-138">Formularz jest sprawdzany przy użyciu `EditContext`formularza .</span><span class="sxs-lookup"><span data-stu-id="924e0-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="924e0-139">Formularz jest dalej weryfikowany przez przekazanie `EditContext` do `ServerValidate` metody, która wywołuje punkt końcowy interfejsu API sieci web na serwerze ( nie*pokazano*).</span><span class="sxs-lookup"><span data-stu-id="924e0-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="924e0-140">Dodatkowy kod jest uruchamiany w zależności od wyniku sprawdzania poprawności `isValid`po stronie klienta i serwera przez sprawdzenie.</span><span class="sxs-lookup"><span data-stu-id="924e0-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="924e0-141">InputText na podstawie zdarzenia wejściowego</span><span class="sxs-lookup"><span data-stu-id="924e0-141">InputText based on the input event</span></span>

<span data-ttu-id="924e0-142">Składnik `InputText` służy do tworzenia składnika niestandardowego, który używa `input` zdarzenia zamiast `change` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="924e0-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="924e0-143">Utwórz komponent z następującymi znacznikami i `InputText` użyj składnika tak, jak jest używany:</span><span class="sxs-lookup"><span data-stu-id="924e0-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="924e0-144">Praca z przyciskami radiowymi</span><span class="sxs-lookup"><span data-stu-id="924e0-144">Work with radio buttons</span></span>

<span data-ttu-id="924e0-145">Podczas pracy z przyciskami radiowymi w formularzu powiązanie danych jest obsługiwane inaczej niż inne elementy, ponieważ przyciski radiowe są oceniane jako grupa.</span><span class="sxs-lookup"><span data-stu-id="924e0-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="924e0-146">Wartość każdego przycisku radiowego jest stała, ale wartość grupy przycisków opcji jest wartością wybranego przycisku radiowego.</span><span class="sxs-lookup"><span data-stu-id="924e0-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="924e0-147">W poniższym przykładzie pokazano, jak:</span><span class="sxs-lookup"><span data-stu-id="924e0-147">The following example shows how to:</span></span>

* <span data-ttu-id="924e0-148">Obsługa powiązania danych dla grupy przycisków opcji.</span><span class="sxs-lookup"><span data-stu-id="924e0-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="924e0-149">Obsługa sprawdzania poprawności `InputRadio` przy użyciu składnika niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="924e0-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="924e0-150">Poniższy `EditForm` element używa `InputRadio` poprzedniego składnika do uzyskania i sprawdzenia poprawności oceny od użytkownika:</span><span class="sxs-lookup"><span data-stu-id="924e0-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

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

## <a name="validation-support"></a><span data-ttu-id="924e0-151">Obsługa sprawdzania poprawności</span><span class="sxs-lookup"><span data-stu-id="924e0-151">Validation support</span></span>

<span data-ttu-id="924e0-152">Składnik `DataAnnotationsValidator` dołącza obsługę sprawdzania poprawności przy użyciu adnotacji danych do kaskadowego `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="924e0-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="924e0-153">Włączenie obsługi sprawdzania poprawności przy użyciu adnotacji danych wymaga tego jawnego gestu.</span><span class="sxs-lookup"><span data-stu-id="924e0-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="924e0-154">Aby użyć innego systemu sprawdzania poprawności niż `DataAnnotationsValidator` adnotacje danych, zastąp implementacją niestandardową.</span><span class="sxs-lookup"><span data-stu-id="924e0-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="924e0-155">Implementacja ASP.NET Core jest dostępna do inspekcji w źródle referencyjnym: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="924e0-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="924e0-156">wykonuje dwa typy walidacji:</span><span class="sxs-lookup"><span data-stu-id="924e0-156"> performs two types of validation:</span></span>

* <span data-ttu-id="924e0-157">*Sprawdzanie poprawności pola* jest wykonywane, gdy użytkownik wyjmuje z pola.</span><span class="sxs-lookup"><span data-stu-id="924e0-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="924e0-158">Podczas sprawdzania poprawności `DataAnnotationsValidator` pola składnik kojarzy wszystkie zgłoszone wyniki sprawdzania poprawności z tym polem.</span><span class="sxs-lookup"><span data-stu-id="924e0-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="924e0-159">*Sprawdzanie poprawności modelu* jest wykonywane, gdy użytkownik przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="924e0-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="924e0-160">Podczas sprawdzania poprawności `DataAnnotationsValidator` modelu składnik próbuje określić pole na podstawie nazwy elementu członkowskiego, który raportuje wynik sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="924e0-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="924e0-161">Wyniki sprawdzania poprawności, które nie są skojarzone z poszczególnych elementów członkowskich są skojarzone z modelu, a nie pola.</span><span class="sxs-lookup"><span data-stu-id="924e0-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="924e0-162">Podsumowanie sprawdzania poprawności i sprawdzanie poprawności składników komunikatu</span><span class="sxs-lookup"><span data-stu-id="924e0-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="924e0-163">Składnik `ValidationSummary` podsumowuje wszystkie komunikaty sprawdzania poprawności, które są podobne do [Pomocnika znacznika podsumowania sprawdzania poprawności:](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="924e0-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="924e0-164">Komunikaty sprawdzania poprawności danych `Model` wyjściowych dla określonego modelu z parametrem:</span><span class="sxs-lookup"><span data-stu-id="924e0-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="924e0-165">Składnik `ValidationMessage` wyświetla komunikaty sprawdzania poprawności dla określonego pola, które jest podobne do [Pomocnika tagu wiadomości sprawdzania poprawności](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="924e0-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="924e0-166">Określ pole sprawdzania `For` poprawności za pomocą atrybutu i wyrażenia lambda nazewnictwa właściwości modelu:</span><span class="sxs-lookup"><span data-stu-id="924e0-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="924e0-167">Składniki `ValidationMessage` `ValidationSummary` i obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="924e0-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="924e0-168">Każdy atrybut, który nie pasuje do parametru `<div>` składnika jest dodawany do wygenerowanego lub `<ul>` elementu.</span><span class="sxs-lookup"><span data-stu-id="924e0-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="924e0-169">Niestandardowe atrybuty sprawdzania poprawności</span><span class="sxs-lookup"><span data-stu-id="924e0-169">Custom validation attributes</span></span>

<span data-ttu-id="924e0-170">Aby upewnić się, że wynik sprawdzania poprawności jest poprawnie skojarzony z polem <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> podczas korzystania <xref:System.ComponentModel.DataAnnotations.ValidationResult>z [niestandardowego atrybutu sprawdzania poprawności,](xref:mvc/models/validation#custom-attributes)przekaż kontekst sprawdzania poprawności podczas tworzenia:</span><span class="sxs-lookup"><span data-stu-id="924e0-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="924e0-171">pakiet sprawdzania poprawności adnotacji danych</span><span class="sxs-lookup"><span data-stu-id="924e0-171"> data annotations validation package</span></span>

<span data-ttu-id="924e0-172">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) to pakiet, który wypełnia luki w `DataAnnotationsValidator` zabezpieczeniach za pomocą składnika.</span><span class="sxs-lookup"><span data-stu-id="924e0-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="924e0-173">Pakiet jest obecnie *eksperymentalny*.</span><span class="sxs-lookup"><span data-stu-id="924e0-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="924e0-174">Atrybut [CompareProperty]</span><span class="sxs-lookup"><span data-stu-id="924e0-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="924e0-175">Nie <xref:System.ComponentModel.DataAnnotations.CompareAttribute> działa dobrze ze `DataAnnotationsValidator` składnikiem, ponieważ nie kojarzy wynik sprawdzania poprawności z określonym elementem członkowskim.</span><span class="sxs-lookup"><span data-stu-id="924e0-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="924e0-176">Może to spowodować niespójne zachowanie między sprawdzanie poprawności na poziomie pola i po weryfikacji całego modelu na przesyłanie.</span><span class="sxs-lookup"><span data-stu-id="924e0-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="924e0-177">Pakiet *eksperymentalny* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) wprowadza dodatkowy atrybut `ComparePropertyAttribute`sprawdzania poprawności, który działa wokół tych ograniczeń.</span><span class="sxs-lookup"><span data-stu-id="924e0-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="924e0-178">W Blazor aplikacji `[CompareProperty]` jest bezpośrednim zamiennikiem `[Compare]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="924e0-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="924e0-179">Modele zagnieżdżone, typy kolekcji i typy złożone</span><span class="sxs-lookup"><span data-stu-id="924e0-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="924e0-180">zapewnia obsługę sprawdzania poprawności danych wejściowych przy użyciu `DataAnnotationsValidator`adnotacji danych z wbudowanym programem .</span><span class="sxs-lookup"><span data-stu-id="924e0-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="924e0-181">Jednak `DataAnnotationsValidator` tylko sprawdza poprawność właściwości najwyższego poziomu modelu powiązanego z formularzem, które nie są właściwościami typu kolekcji lub kompleksu.</span><span class="sxs-lookup"><span data-stu-id="924e0-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="924e0-182">Aby sprawdzić poprawność całego wykresu obiektu modelu powiązanego, w tym właściwości `ObjectGraphDataAnnotationsValidator` typu kolekcji i kompleksu, należy użyć dostarczonego przez *eksperymentalny* pakiet [Microsoft.AspNetCore.Components.DataAnnotations.Validation:](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)</span><span class="sxs-lookup"><span data-stu-id="924e0-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="924e0-183">Dodawanie adnotacji do `[ValidateComplexType]`właściwości modelu za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="924e0-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="924e0-184">W następujących klasach `ShipDescription` modelu klasa zawiera dodatkowe adnotacje danych do sprawdzania poprawności, gdy model jest powiązany z formularzem:</span><span class="sxs-lookup"><span data-stu-id="924e0-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="924e0-185">*Starship.cs:*</span><span class="sxs-lookup"><span data-stu-id="924e0-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="924e0-186">*ShipDescription.cs:*</span><span class="sxs-lookup"><span data-stu-id="924e0-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="924e0-187">Włącz przycisk przesyłania na podstawie sprawdzania poprawności formularza</span><span class="sxs-lookup"><span data-stu-id="924e0-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="924e0-188">Aby włączyć i wyłączyć przycisk przesyłania na podstawie sprawdzania poprawności formularza:</span><span class="sxs-lookup"><span data-stu-id="924e0-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="924e0-189">Formularz służy `EditContext` do przypisywania modelu podczas inicjowania komponentu.</span><span class="sxs-lookup"><span data-stu-id="924e0-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="924e0-190">Sprawdź poprawność formularza w `OnFieldChanged` wywołaniu zwrotnym kontekstu, aby włączyć i wyłączyć przycisk przesyłania.</span><span class="sxs-lookup"><span data-stu-id="924e0-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="924e0-191">Odłącz program obsługi zdarzeń `Dispose` w metodzie.</span><span class="sxs-lookup"><span data-stu-id="924e0-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="924e0-192">Aby uzyskać więcej informacji, zobacz <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="924e0-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="924e0-193">W poprzednim przykładzie `_formInvalid` ustaw, czy: `false`</span><span class="sxs-lookup"><span data-stu-id="924e0-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="924e0-194">Formularz jest wstępnie załadowany z prawidłowymi wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="924e0-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="924e0-195">Przycisk prześlij ma być włączony po załadowaniu formularza.</span><span class="sxs-lookup"><span data-stu-id="924e0-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="924e0-196">Efektem ubocznym poprzedniego podejścia jest `ValidationSummary` to, że składnik jest wypełniany nieprawidłowymi polami po interakcji użytkownika z dowolnym jednym polem.</span><span class="sxs-lookup"><span data-stu-id="924e0-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="924e0-197">Ten scenariusz można rozwiązać w jeden z następujących sposobów:</span><span class="sxs-lookup"><span data-stu-id="924e0-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="924e0-198">Nie używaj `ValidationSummary` składnika w formularzu.</span><span class="sxs-lookup"><span data-stu-id="924e0-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="924e0-199">Spraw, `ValidationSummary` aby komponent był widoczny po wybraniu przycisku przesyłania (na przykład w metodzie). `HandleValidSubmit`</span><span class="sxs-lookup"><span data-stu-id="924e0-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
