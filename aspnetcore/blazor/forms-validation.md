---
title: 'ASP.NET Core Blazor formularzy i walidacji'
author: guardrex
description: 'Dowiedz się, jak używać scenariuszy i walidacji pól w programie Blazor .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/forms-validation
ms.openlocfilehash: fe232b40a2255732dd375cc266937576d5b2d5d9
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507827"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="931e6-103">ASP.NET Core Blazor formularzy i walidacji</span><span class="sxs-lookup"><span data-stu-id="931e6-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="931e6-104">[Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="931e6-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="931e6-105">Formularze i walidacje są obsługiwane w przypadku Blazor używania [adnotacji danych](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="931e6-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="931e6-106">Następujący `ExampleModel` Typ definiuje logikę walidacji przy użyciu adnotacji danych:</span><span class="sxs-lookup"><span data-stu-id="931e6-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="931e6-107">Formularz jest definiowany przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składnika.</span><span class="sxs-lookup"><span data-stu-id="931e6-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="931e6-108">W poniższej formie przedstawiono typowe elementy, składniki i Razor kod:</span><span class="sxs-lookup"><span data-stu-id="931e6-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="931e6-109">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="931e6-109">In the preceding example:</span></span>

* <span data-ttu-id="931e6-110">Formularz sprawdza poprawność danych wejściowych użytkownika w `name` polu przy użyciu walidacji zdefiniowanej w `ExampleModel` typie.</span><span class="sxs-lookup"><span data-stu-id="931e6-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="931e6-111">Model jest tworzony w `@code` bloku składnika i przechowywany w prywatnym polu ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="931e6-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="931e6-112">Pole jest przypisane do `Model` atrybutu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="931e6-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="931e6-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Powiązania składnika:</span><span class="sxs-lookup"><span data-stu-id="931e6-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="931e6-114">Właściwość modelu ( `exampleModel.Name` ) do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="931e6-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="931e6-115">Aby uzyskać więcej informacji na temat powiązania właściwości, zobacz <xref:blazor/components/data-binding#binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="931e6-115">For more information on property binding, see <xref:blazor/components/data-binding#binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="931e6-116">Delegowanie zdarzenia zmiany do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="931e6-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="931e6-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [Składnik walidacji](#validator-components) dołącza obsługę walidacji przy użyciu adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="931e6-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="931e6-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje komunikaty weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="931e6-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="931e6-119">`HandleValidSubmit` jest wyzwalany po pomyślnym przesłaniu formularza (kończy walidację).</span><span class="sxs-lookup"><span data-stu-id="931e6-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="931e6-120">Wbudowane składniki formularzy</span><span class="sxs-lookup"><span data-stu-id="931e6-120">Built-in forms components</span></span>

<span data-ttu-id="931e6-121">Zestaw wbudowanych składników jest dostępny do odbierania i weryfikowania danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="931e6-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="931e6-122">Dane wejściowe są weryfikowane po ich zmianie i po przesłaniu formularza.</span><span class="sxs-lookup"><span data-stu-id="931e6-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="931e6-123">W poniższej tabeli przedstawiono dostępne składniki danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="931e6-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="931e6-124">Składnik wejściowy</span><span class="sxs-lookup"><span data-stu-id="931e6-124">Input component</span></span> | <span data-ttu-id="931e6-125">Renderowane jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="931e6-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="931e6-126">Składnik wejściowy</span><span class="sxs-lookup"><span data-stu-id="931e6-126">Input component</span></span> | <span data-ttu-id="931e6-127">Renderowane jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="931e6-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="931e6-128">`InputRadio`Składniki i `InputRadioGroup` są dostępne w ASP.NET Core 5,0 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="931e6-128">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="931e6-129">Aby uzyskać więcej informacji, wybierz 5,0 lub nowszą wersję tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="931e6-129">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="931e6-130">Wszystkie składniki danych wejściowych, w tym <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="931e6-130">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="931e6-131">Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do renderowanego elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="931e6-131">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="931e6-132">Składniki wejściowe zapewniają domyślne zachowanie podczas sprawdzania poprawności, gdy pole jest zmieniane, włącznie z aktualizacją klasy CSS pola w celu odzwierciedlenia stanu pola.</span><span class="sxs-lookup"><span data-stu-id="931e6-132">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="931e6-133">Niektóre składniki obejmują przydatne logiki analizy.</span><span class="sxs-lookup"><span data-stu-id="931e6-133">Some components include useful parsing logic.</span></span> <span data-ttu-id="931e6-134">Na przykład i bezproblemowo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> obsługiwać wartości nieanalizowane przez rejestrowanie wartości, które nie są analizowane jako błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="931e6-134">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="931e6-135">Typy, które mogą akceptować wartości null, obsługują również wartość null pola docelowego (na przykład `int?` ).</span><span class="sxs-lookup"><span data-stu-id="931e6-135">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="931e6-136">Następujący `Starship` Typ definiuje logikę walidacji przy użyciu większego zestawu właściwości i adnotacji danych niż wcześniej `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="931e6-136">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="931e6-137">W powyższym przykładzie `Description` jest opcjonalne, ponieważ nie są obecne adnotacje danych.</span><span class="sxs-lookup"><span data-stu-id="931e6-137">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="931e6-138">Następujący formularz sprawdza poprawność danych wejściowych użytkownika przy użyciu weryfikacji zdefiniowanej w `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="931e6-138">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
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
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="931e6-139"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Tworzy <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [wartość kaskadową](xref:blazor/components/cascading-values-and-parameters) , która śledzi metadane dotyczące procesu edycji, w tym pola, które zostały zmodyfikowane i bieżące komunikaty weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="931e6-139">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="931e6-140">Przypisz **albo** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="931e6-140">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="931e6-141">Przypisanie obu nie jest obsługiwane i generuje **błąd czasu wykonania**.</span><span class="sxs-lookup"><span data-stu-id="931e6-141">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="931e6-142"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Zapewnia wygodne zdarzenia dla prawidłowego i nieprawidłowego przesłania formularza:</span><span class="sxs-lookup"><span data-stu-id="931e6-142">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="931e6-143">Użyj <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> , aby użyć niestandardowego kodu do wyzwalania walidacji i sprawdzania wartości pól.</span><span class="sxs-lookup"><span data-stu-id="931e6-143">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="931e6-144">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="931e6-144">In the following example:</span></span>

* <span data-ttu-id="931e6-145">`HandleSubmit`Metoda jest wykonywana, gdy **`Submit`** przycisk jest zaznaczony.</span><span class="sxs-lookup"><span data-stu-id="931e6-145">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="931e6-146">Formularz jest weryfikowany przez wywołanie <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="931e6-146">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="931e6-147">Dodatkowy kod jest wykonywany w zależności od wyniku walidacji.</span><span class="sxs-lookup"><span data-stu-id="931e6-147">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="931e6-148">Umieść logikę biznesową w metodzie przypisanej do <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .</span><span class="sxs-lookup"><span data-stu-id="931e6-148">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="931e6-149">Interfejs API struktury nie istnieje, aby wyczyścić komunikaty sprawdzania poprawności bezpośrednio z programu <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="931e6-149">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="931e6-150">W związku z tym zazwyczaj nie zaleca się dodawania komunikatów weryfikacyjnych do nowego <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> w formularzu.</span><span class="sxs-lookup"><span data-stu-id="931e6-150">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="931e6-151">Aby zarządzać komunikatami sprawdzania poprawności, należy użyć [składnika modułu sprawdzania](#validator-components) [poprawności kodu weryfikacyjnego logiki biznesowej](#business-logic-validation), zgodnie z opisem w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="931e6-151">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="931e6-152">Obsługa nazw wyświetlanych</span><span class="sxs-lookup"><span data-stu-id="931e6-152">Display name support</span></span>

<span data-ttu-id="931e6-153">*Ta sekcja ma zastosowanie do ASP.NET Core w programie .NET 5 Release Candidate 1 (RC1) lub nowszym.*</span><span class="sxs-lookup"><span data-stu-id="931e6-153">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="931e6-154">Następujące wbudowane składniki obsługują nazwy wyświetlane z `DisplayName` parametrem:</span><span class="sxs-lookup"><span data-stu-id="931e6-154">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="931e6-155">W poniższym `InputDate` przykładzie składnika:</span><span class="sxs-lookup"><span data-stu-id="931e6-155">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="931e6-156">Nazwa wyświetlana ( `DisplayName` ) jest ustawiona na `birthday` .</span><span class="sxs-lookup"><span data-stu-id="931e6-156">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="931e6-157">Składnik jest powiązany z `BirthDate` właściwością jako `DateTime` typem.</span><span class="sxs-lookup"><span data-stu-id="931e6-157">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="931e6-158">Jeśli użytkownik nie poda wartości typu Date, komunikat o błędzie walidacji zostanie wyświetlony w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="931e6-158">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="931e6-159">Składniki modułu sprawdzania poprawności</span><span class="sxs-lookup"><span data-stu-id="931e6-159">Validator components</span></span>

<span data-ttu-id="931e6-160">Składnik modułu sprawdzania poprawności obsługuje walidację formularza przez zarządzanie <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> dla formularza <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="931e6-160">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="931e6-161">BlazorStruktura zapewnia <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik umożliwiający dołączenie obsługi walidacji do formularzy na podstawie [atrybutów walidacji (adnotacje danych)](xref:mvc/models/validation#validation-attributes).</span><span class="sxs-lookup"><span data-stu-id="931e6-161">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="931e6-162">Utwórz niestandardowe składniki modułu sprawdzania poprawności, aby przetwarzać komunikaty weryfikacyjne dla różnych formularzy na tej samej stronie lub w tym samym formularzu w różnych krokach przetwarzania formularzy, na przykład Walidacja po stronie klienta, a następnie weryfikacja po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="931e6-162">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="931e6-163">Przykład składnika modułu sprawdzania poprawności przedstawiony w tej sekcji `CustomValidator` jest używany w następujących sekcjach tego artykułu:</span><span class="sxs-lookup"><span data-stu-id="931e6-163">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="931e6-164">Sprawdzanie poprawności logiki biznesowej</span><span class="sxs-lookup"><span data-stu-id="931e6-164">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="931e6-165">Sprawdzanie poprawności serwera</span><span class="sxs-lookup"><span data-stu-id="931e6-165">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="931e6-166">Atrybuty walidacji niestandardowych adnotacji danych mogą być używane zamiast niestandardowych składników modułu sprawdzania poprawności w wielu przypadkach.</span><span class="sxs-lookup"><span data-stu-id="931e6-166">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="931e6-167">Atrybuty niestandardowe zastosowane do modelu formularza aktywują się przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="931e6-167">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="931e6-168">Gdy jest używany z walidacją po stronie serwera, wszystkie atrybuty niestandardowe zastosowane do modelu muszą być wykonywalne na serwerze.</span><span class="sxs-lookup"><span data-stu-id="931e6-168">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="931e6-169">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="931e6-169">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="931e6-170">Utwórz składnik modułu sprawdzania poprawności z <xref:Microsoft.AspNetCore.Components.ComponentBase> :</span><span class="sxs-lookup"><span data-stu-id="931e6-170">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="931e6-171">Formularz <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jest [parametrem kaskadowym](xref:blazor/components/cascading-values-and-parameters) składnika.</span><span class="sxs-lookup"><span data-stu-id="931e6-171">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="931e6-172">Po zainicjowaniu składnika modułu sprawdzania poprawności zostanie <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> utworzony nowy, aby zachować bieżącą listę błędów.</span><span class="sxs-lookup"><span data-stu-id="931e6-172">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="931e6-173">Magazyn komunikatów otrzymuje błędy, gdy kod dewelopera w składniku formularza wywołuje `DisplayErrors` metodę.</span><span class="sxs-lookup"><span data-stu-id="931e6-173">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="931e6-174">Błędy są przesyłane do `DisplayErrors` metody w [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) .</span><span class="sxs-lookup"><span data-stu-id="931e6-174">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="931e6-175">W słowniku klucz jest nazwą pola formularza, które zawiera co najmniej jeden błąd.</span><span class="sxs-lookup"><span data-stu-id="931e6-175">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="931e6-176">Wartość jest listą błędów.</span><span class="sxs-lookup"><span data-stu-id="931e6-176">The value is the error list.</span></span>
* <span data-ttu-id="931e6-177">Komunikaty są czyszczone w przypadku wystąpienia któregokolwiek z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="931e6-177">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="931e6-178"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>Gdy <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> zdarzenie zostanie zgłoszone, zażądano walidacji.</span><span class="sxs-lookup"><span data-stu-id="931e6-178">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="931e6-179">Wszystkie błędy są wyczyszczone.</span><span class="sxs-lookup"><span data-stu-id="931e6-179">All of the errors are cleared.</span></span>
  * <span data-ttu-id="931e6-180">Pole zostanie zmienione w formularzu, gdy <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> zdarzenie zostanie zgłoszone.</span><span class="sxs-lookup"><span data-stu-id="931e6-180">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="931e6-181">Tylko błędy dla pola są wyczyszczone.</span><span class="sxs-lookup"><span data-stu-id="931e6-181">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="931e6-182">`ClearErrors`Metoda jest wywoływana przez kod dewelopera.</span><span class="sxs-lookup"><span data-stu-id="931e6-182">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="931e6-183">Wszystkie błędy są wyczyszczone.</span><span class="sxs-lookup"><span data-stu-id="931e6-183">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

namespace BlazorSample.Client
{
    public class CustomValidator : ComponentBase
    {
        private ValidationMessageStore messageStore;

        [CascadingParameter]
        private EditContext CurrentEditContext { get; set; }

        protected override void OnInitialized()
        {
            if (CurrentEditContext == null)
            {
                throw new InvalidOperationException(
                    $"{nameof(CustomValidator)} requires a cascading " +
                    $"parameter of type {nameof(EditContext)}. " +
                    $"For example, you can use {nameof(CustomValidator)} " +
                    $"inside an {nameof(EditForm)}.");
            }

            messageStore = new ValidationMessageStore(CurrentEditContext);

            CurrentEditContext.OnValidationRequested += (s, e) => 
                messageStore.Clear();
            CurrentEditContext.OnFieldChanged += (s, e) => 
                messageStore.Clear(e.FieldIdentifier);
        }

        public void DisplayErrors(Dictionary<string, List<string>> errors)
        {
            foreach (var err in errors)
            {
                messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
            }

            CurrentEditContext.NotifyValidationStateChanged();
        }

        public void ClearErrors()
        {
            messageStore.Clear();
            CurrentEditContext.NotifyValidationStateChanged();
        }
    }
}
```

## <a name="business-logic-validation"></a><span data-ttu-id="931e6-184">Sprawdzanie poprawności logiki biznesowej</span><span class="sxs-lookup"><span data-stu-id="931e6-184">Business logic validation</span></span>

<span data-ttu-id="931e6-185">Walidacja logiki biznesowej można wykonać za pomocą [składnika modułu sprawdzania](#validator-components) poprawności, który odbiera błędy formularza w słowniku.</span><span class="sxs-lookup"><span data-stu-id="931e6-185">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="931e6-186">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="931e6-186">In the following example:</span></span>

* <span data-ttu-id="931e6-187">`CustomValidator`Składnik składnika [walidacji](#validator-components) w tym artykule jest używany.</span><span class="sxs-lookup"><span data-stu-id="931e6-187">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="931e6-188">Walidacja wymaga wartości dla opisu wysyłki ( `Description` ), jeśli użytkownik wybierze `Defense` klasyfikację statku ( `Classification` ).</span><span class="sxs-lookup"><span data-stu-id="931e6-188">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="931e6-189">Gdy w składniku są ustawiane komunikaty weryfikacji, są one dodawane do modułu sprawdzania poprawności <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> i wyświetlane w <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :</span><span class="sxs-lookup"><span data-stu-id="931e6-189">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="931e6-190">Jako alternatywę dla korzystania ze [składników walidacji](#validator-components), można użyć atrybutów walidacji adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="931e6-190">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="931e6-191">Atrybuty niestandardowe zastosowane do modelu formularza aktywują się przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="931e6-191">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="931e6-192">Gdy jest używany z walidacją po stronie serwera, atrybuty muszą być wykonywalne na serwerze.</span><span class="sxs-lookup"><span data-stu-id="931e6-192">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="931e6-193">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="931e6-193">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="931e6-194">Sprawdzanie poprawności serwera</span><span class="sxs-lookup"><span data-stu-id="931e6-194">Server validation</span></span>

<span data-ttu-id="931e6-195">Sprawdzanie poprawności serwera można wykonać za pomocą [składnika modułu sprawdzania poprawności](#validator-components)serwera:</span><span class="sxs-lookup"><span data-stu-id="931e6-195">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="931e6-196">Przetwarzaj sprawdzanie poprawności po stronie klienta w formie ze <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnikiem.</span><span class="sxs-lookup"><span data-stu-id="931e6-196">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="931e6-197">Gdy formularz przejdzie do walidacji po stronie klienta ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> jest wywoływana), Wyślij <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> do interfejsu API serwera wewnętrznej bazy danych na potrzeby przetwarzania formularza.</span><span class="sxs-lookup"><span data-stu-id="931e6-197">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="931e6-198">Sprawdzanie poprawności modelu procesu na serwerze.</span><span class="sxs-lookup"><span data-stu-id="931e6-198">Process model validation on the server.</span></span>
* <span data-ttu-id="931e6-199">Interfejs API serwera zawiera zarówno wbudowaną, jak i niestandardową logikę sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="931e6-199">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="931e6-200">Jeśli walidacja kończy się na serwerze, należy przetworzyć formularz i ponownie wysłać kod stanu sukcesu ( *200-OK* ).</span><span class="sxs-lookup"><span data-stu-id="931e6-200">If validation passes on the server, process the form and send back a success status code ( *200 - OK* ).</span></span> <span data-ttu-id="931e6-201">Jeśli walidacja nie powiedzie się, zwróć kod stanu błędu ( *400-złe żądanie* ) i błędy walidacji pola.</span><span class="sxs-lookup"><span data-stu-id="931e6-201">If validation fails, return a failure status code ( *400 - Bad Request* ) and the field validation errors.</span></span>
* <span data-ttu-id="931e6-202">Wyłącz formularz po powodzeniu lub Wyświetl błędy.</span><span class="sxs-lookup"><span data-stu-id="931e6-202">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="931e6-203">Poniższy przykład jest oparty na:</span><span class="sxs-lookup"><span data-stu-id="931e6-203">The following example is based on:</span></span>

* <span data-ttu-id="931e6-204">Rozwiązanie hostowane Blazor utworzone przez [ Blazor szablon hostowanego projektu](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="931e6-204">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="931e6-205">Przykład może być używany z dowolnym bezpiecznymi Blazor rozwiązaniami obsługiwanymi opisanymi w temacie [zabezpieczenia i Identity Dokumentacja](xref:blazor/security/webassembly/index#implementation-guidance).</span><span class="sxs-lookup"><span data-stu-id="931e6-205">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="931e6-206">Przykładowa *baza danych Starfleet Starship* w poprzedniej sekcji [wbudowanych elementów formularzy](#built-in-forms-components) .</span><span class="sxs-lookup"><span data-stu-id="931e6-206">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="931e6-207">Blazor <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Składnik struktury.</span><span class="sxs-lookup"><span data-stu-id="931e6-207">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="931e6-208">`CustomValidator`Składnik widoczny w sekcji [składniki modułu sprawdzania poprawności](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="931e6-208">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="931e6-209">W poniższym przykładzie interfejs API serwera sprawdza, czy wartość jest podana dla opisu odbiorcy ( `Description` ), jeśli użytkownik wybierze `Defense` klasyfikację statku ( `Classification` ).</span><span class="sxs-lookup"><span data-stu-id="931e6-209">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="931e6-210">Umieść `Starship` model w projekcie rozwiązania, `Shared` tak aby aplikacje klienta i serwera mogły korzystać z modelu.</span><span class="sxs-lookup"><span data-stu-id="931e6-210">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="931e6-211">Ponieważ model wymaga adnotacji danych, Dodaj odwołanie do pakietu dla [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) `Shared` pliku projektu projektu:</span><span class="sxs-lookup"><span data-stu-id="931e6-211">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="931e6-212">Aby określić najnowszą wersję pakietu, która nie jest w wersji zapoznawczej, zobacz **historię wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span><span class="sxs-lookup"><span data-stu-id="931e6-212">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="931e6-213">W projekcie interfejsu API serwera Dodaj kontroler, aby przetwarzać żądania weryfikacji Starship ( `Controllers/StarshipValidation.cs` ) i zwracać nieudane komunikaty weryfikacji:</span><span class="sxs-lookup"><span data-stu-id="931e6-213">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace BlazorSample.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="931e6-214">Gdy na serwerze wystąpi błąd walidacji powiązania modelu, obiekt [`ApiController`](xref:web-api/index) ( <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ) zwykle zwraca domyślną nieprawidłową [odpowiedź na żądanie](xref:web-api/index#default-badrequest-response) z <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="931e6-214">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="931e6-215">Odpowiedź zawiera więcej danych niż tylko błędy walidacji, jak pokazano w poniższym przykładzie, gdy nie przesłano wszystkich pól formularza *bazy danych Starfleet Starship* i niepowodzenie walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="931e6-215">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="931e6-216">Jeśli interfejs API serwera zwróci poprzednią domyślną odpowiedź JSON, możliwe jest, aby klient przeanalizować odpowiedź w celu uzyskania elementów podrzędnych `errors` węzła.</span><span class="sxs-lookup"><span data-stu-id="931e6-216">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="931e6-217">Nie jest to jednak wygodne do analizy pliku.</span><span class="sxs-lookup"><span data-stu-id="931e6-217">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="931e6-218">Analizowanie pliku JSON wymaga dodatkowego kodu po wywołaniu <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> w celu wygenerowania [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) błędów podczas przetwarzania błędów walidacji formularzy.</span><span class="sxs-lookup"><span data-stu-id="931e6-218">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="931e6-219">W idealnym przypadku interfejs API serwera powinien zwrócić tylko błędy walidacji:</span><span class="sxs-lookup"><span data-stu-id="931e6-219">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="931e6-220">Aby zmodyfikować odpowiedź interfejsu API serwera tak, aby zwracała błędy walidacji, należy zmienić delegata, który jest wywoływany w akcjach, które są adnotacją <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="931e6-220">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="931e6-221">Dla punktu końcowego interfejsu API ( `/StarshipValidation` ) Zwróć wartość <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> .</span><span class="sxs-lookup"><span data-stu-id="931e6-221">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="931e6-222">W przypadku innych punktów końcowych interfejsu API Zachowaj zachowanie domyślne, zwracając wynik obiektu z nowym <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :</span><span class="sxs-lookup"><span data-stu-id="931e6-222">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="931e6-223">Aby uzyskać więcej informacji, zobacz <xref:web-api/handle-errors#validation-failure-error-response>.</span><span class="sxs-lookup"><span data-stu-id="931e6-223">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="931e6-224">W projekcie klienta Dodaj składnik walidatora widoczny w sekcji [składniki modułu sprawdzania poprawności](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="931e6-224">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="931e6-225">W projekcie klienta formularz *bazy danych Starfleet Starship* został zaktualizowany w celu wyświetlenia błędów walidacji serwera i pomocy `CustomValidator` składnika.</span><span class="sxs-lookup"><span data-stu-id="931e6-225">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="931e6-226">Gdy interfejs API serwera zwraca komunikaty weryfikacyjne, są one dodawane do `CustomValidator` składnika <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> .</span><span class="sxs-lookup"><span data-stu-id="931e6-226">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="931e6-227">Błędy są dostępne w formularzu <xref:Microsoft.AspNetCore.Components.Forms.EditContext> na potrzeby wyświetlania według formularza <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :</span><span class="sxs-lookup"><span data-stu-id="931e6-227">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="931e6-228">Jako alternatywę dla [składników walidacji](#validator-components), można użyć atrybutów walidacji adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="931e6-228">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="931e6-229">Atrybuty niestandardowe zastosowane do modelu formularza aktywują się przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="931e6-229">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="931e6-230">Gdy jest używany z walidacją po stronie serwera, atrybuty muszą być wykonywalne na serwerze.</span><span class="sxs-lookup"><span data-stu-id="931e6-230">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="931e6-231">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="931e6-231">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="931e6-232">Podejście do walidacji po stronie serwera w tej sekcji jest odpowiednie dla dowolnego z Blazor WebAssembly przykładów obsługiwanych rozwiązań w tym zestawie dokumentacji:</span><span class="sxs-lookup"><span data-stu-id="931e6-232">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="931e6-233">Usługa Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="931e6-233">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="931e6-234">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="931e6-234">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="931e6-235">Identity Server</span><span class="sxs-lookup"><span data-stu-id="931e6-235">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="931e6-236">InputText na podstawie zdarzenia wejściowego</span><span class="sxs-lookup"><span data-stu-id="931e6-236">InputText based on the input event</span></span>

<span data-ttu-id="931e6-237">Użyj <xref:Microsoft.AspNetCore.Components.Forms.InputText> składnika, aby utworzyć niestandardowy składnik, który używa `input` zdarzenia zamiast `change` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="931e6-237">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="931e6-238">W poniższym przykładzie `CustomInputText` składnik dziedziczy `InputText` składnik platformy i ustawia powiązanie zdarzenia ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) na `oninput` zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="931e6-238">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="931e6-239">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="931e6-239">`Shared/CustomInputText.razor`:</span></span>

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

<span data-ttu-id="931e6-240">`CustomInputText`Składnika można używać wszędzie tam, gdzie <xref:Microsoft.AspNetCore.Components.Forms.InputText> są używane:</span><span class="sxs-lookup"><span data-stu-id="931e6-240">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="931e6-241">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="931e6-241">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
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
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="931e6-242">Przyciski radiowe</span><span class="sxs-lookup"><span data-stu-id="931e6-242">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="931e6-243">Użyj `InputRadio` składników ze składnikiem, `InputRadioGroup` Aby utworzyć grupę przycisków radiowych.</span><span class="sxs-lookup"><span data-stu-id="931e6-243">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="931e6-244">W poniższym przykładzie właściwości są dodawane do `Starship` modelu opisanego w sekcji [wbudowane składniki formularzy](#built-in-forms-components) :</span><span class="sxs-lookup"><span data-stu-id="931e6-244">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="931e6-245">Dodaj następujące elementy `enums` do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="931e6-245">Add the following `enums` to the app.</span></span> <span data-ttu-id="931e6-246">Utwórz nowy plik do przechowywania `enums` lub Dodaj `enums` do `Starship.cs` pliku.</span><span class="sxs-lookup"><span data-stu-id="931e6-246">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="931e6-247">Utwórz `enums` dostęp do `Starship` modelu i formularz *bazy danych Starfleet Starship* :</span><span class="sxs-lookup"><span data-stu-id="931e6-247">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="931e6-248">Aktualizowanie formularza *bazy danych Starfleet Starship* opisanej w sekcji [wbudowane składniki formularzy](#built-in-forms-components) .</span><span class="sxs-lookup"><span data-stu-id="931e6-248">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="931e6-249">Dodaj składniki do produkcji:</span><span class="sxs-lookup"><span data-stu-id="931e6-249">Add the components to produce:</span></span>

* <span data-ttu-id="931e6-250">Grupa przycisków radiowych dla producenta statku.</span><span class="sxs-lookup"><span data-stu-id="931e6-250">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="931e6-251">Zagnieżdżona grupa przycisków radiowych dla kolorów i aparatu.</span><span class="sxs-lookup"><span data-stu-id="931e6-251">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="931e6-252">Jeśli `Name` parametr zostanie pominięty, `InputRadio` składniki są pogrupowane według ostatniego elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="931e6-252">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="931e6-253">Podczas pracy z przyciskami radiowymi w formularzu powiązanie danych jest obsługiwane inaczej niż inne elementy, ponieważ przyciski radiowe są oceniane jako Grupa.</span><span class="sxs-lookup"><span data-stu-id="931e6-253">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="931e6-254">Wartość każdego przycisku radiowego jest stała, ale wartość grupy przycisków radiowych jest wartością wybranego przycisku radiowego.</span><span class="sxs-lookup"><span data-stu-id="931e6-254">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="931e6-255">Poniższy przykład pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="931e6-255">The following example shows how to:</span></span>

* <span data-ttu-id="931e6-256">Obsługa powiązania danych dla grupy przycisków radiowych.</span><span class="sxs-lookup"><span data-stu-id="931e6-256">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="931e6-257">Obsługa walidacji przy użyciu `InputRadio` składnika niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="931e6-257">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="931e6-258">Poniższe <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składniki używają powyższego `InputRadio` składnika do uzyskania i zweryfikowania klasyfikacji od użytkownika:</span><span class="sxs-lookup"><span data-stu-id="931e6-258">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
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
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="931e6-259">`<select>`Opcje elementu powiązania z wartościami obiektów C# `null`</span><span class="sxs-lookup"><span data-stu-id="931e6-259">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="931e6-260">Nie istnieje rozsądny sposób reprezentowania `<select>` wartości opcji elementu jako wartości obiektu języka C# `null` , ponieważ:</span><span class="sxs-lookup"><span data-stu-id="931e6-260">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="931e6-261">Atrybuty HTML nie mogą mieć `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="931e6-261">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="931e6-262">Najbliższy odpowiednik `null` w języku HTML to brak `value` atrybutu HTML z `<option>` elementu.</span><span class="sxs-lookup"><span data-stu-id="931e6-262">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="931e6-263">W przypadku wybrania `<option>` bez `value` atrybutu przeglądarka traktuje wartość jako *zawartość tekstową* `<option>` elementu.</span><span class="sxs-lookup"><span data-stu-id="931e6-263">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="931e6-264">BlazorPlatforma nie próbuje pominąć zachowania domyślnego, ponieważ spowodowałoby to:</span><span class="sxs-lookup"><span data-stu-id="931e6-264">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="931e6-265">Tworzenie łańcucha obejść specjalnych przypadków w strukturze.</span><span class="sxs-lookup"><span data-stu-id="931e6-265">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="931e6-266">Istotne zmiany w bieżącym zachowaniu struktury.</span><span class="sxs-lookup"><span data-stu-id="931e6-266">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="931e6-267">Najbardziej wiarygodny `null` odpowiednik w kodzie HTML jest *ciągiem pustym* `value` .</span><span class="sxs-lookup"><span data-stu-id="931e6-267">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="931e6-268">BlazorPlatforma obsługuje `null` konwersje do pustych ciągów dla dwukierunkowego powiązania z `<select>` wartością.</span><span class="sxs-lookup"><span data-stu-id="931e6-268">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="931e6-269">BlazorStruktura nie jest automatycznie obsługiwana `null` dla pustych konwersji ciągów podczas próby dwukierunkowego powiązania z `<select>` wartością.</span><span class="sxs-lookup"><span data-stu-id="931e6-269">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="931e6-270">Aby uzyskać więcej informacji, zobacz temat [Usuwanie powiązania `<select>` do wartości null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="931e6-270">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="931e6-271">Obsługa walidacji</span><span class="sxs-lookup"><span data-stu-id="931e6-271">Validation support</span></span>

<span data-ttu-id="931e6-272"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Składnik dołącza obsługę walidacji przy użyciu adnotacji danych do kaskadowo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="931e6-272">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="931e6-273">Włączenie obsługi walidacji przy użyciu adnotacji danych wymaga tego jawnego gestu.</span><span class="sxs-lookup"><span data-stu-id="931e6-273">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="931e6-274">Aby użyć innego systemu sprawdzania poprawności niż adnotacje danych, Zastąp zmienną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> implementacją niestandardową.</span><span class="sxs-lookup"><span data-stu-id="931e6-274">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="931e6-275">Implementacja ASP.NET Core jest dostępna do inspekcji w źródle referencyjnym: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="931e6-275">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="931e6-276">Powyższe linki do źródła odniesienia zawierają kod z `master` gałęzi repozytorium, który reprezentuje bieżące programowanie jednostki produktu dla następnej wersji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="931e6-276">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="931e6-277">Aby wybrać gałąź dla innej wersji, użyj selektora gałęzi GitHub (na przykład `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="931e6-277">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="931e6-278">Blazor wykonuje dwa typy walidacji:</span><span class="sxs-lookup"><span data-stu-id="931e6-278">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="931e6-279">*Sprawdzanie poprawności pola* jest wykonywane, gdy użytkownik wyprowadzi karty z pola.</span><span class="sxs-lookup"><span data-stu-id="931e6-279">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="931e6-280">Podczas sprawdzania poprawności pola <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik kojarzy wszystkie zgłoszone wyniki walidacji z polem.</span><span class="sxs-lookup"><span data-stu-id="931e6-280">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="931e6-281">*Sprawdzanie poprawności modelu* jest wykonywane, gdy użytkownik prześle formularz.</span><span class="sxs-lookup"><span data-stu-id="931e6-281">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="931e6-282">Podczas walidacji modelu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik próbuje określić pole na podstawie nazwy elementu członkowskiego, który raportuje wynik sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="931e6-282">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="931e6-283">Wyniki walidacji, które nie są skojarzone z poszczególnymi elementami członkowskimi, są skojarzone z modelem, a nie polem.</span><span class="sxs-lookup"><span data-stu-id="931e6-283">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="931e6-284">Składniki podsumowania walidacji i komunikatów weryfikacji</span><span class="sxs-lookup"><span data-stu-id="931e6-284">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="931e6-285"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje wszystkie komunikaty weryfikacyjne podobne do [pomocnika tagów podsumowania weryfikacji](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="931e6-285">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="931e6-286">Wyprowadź komunikaty weryfikacji dla określonego modelu z `Model` parametrem:</span><span class="sxs-lookup"><span data-stu-id="931e6-286">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="931e6-287"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składnik wyświetla komunikaty sprawdzania poprawności dla określonego pola, które jest podobne do [pomocnika tagów komunikatu weryfikacji](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="931e6-287">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="931e6-288">Określ pole do walidacji z `For` atrybutem i wyrażeniem lambda, które nazywa właściwość modelu:</span><span class="sxs-lookup"><span data-stu-id="931e6-288">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="931e6-289"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składniki i <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="931e6-289">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="931e6-290">Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do wygenerowanego `<div>` `<ul>` elementu or.</span><span class="sxs-lookup"><span data-stu-id="931e6-290">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="931e6-291">Kontroluj styl komunikatów weryfikacyjnych w arkuszu stylów aplikacji ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ).</span><span class="sxs-lookup"><span data-stu-id="931e6-291">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="931e6-292">Klasa domyślna `validation-message` Ustawia kolor tekstu komunikatów walidacji na czerwony:</span><span class="sxs-lookup"><span data-stu-id="931e6-292">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="931e6-293">Niestandardowe atrybuty walidacji</span><span class="sxs-lookup"><span data-stu-id="931e6-293">Custom validation attributes</span></span>

<span data-ttu-id="931e6-294">Aby upewnić się, że wynik walidacji jest prawidłowo skojarzony z polem przy użyciu [niestandardowego atrybutu walidacji](xref:mvc/models/validation#custom-attributes), należy przekazać kontekst walidacji <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> podczas tworzenia <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="931e6-294">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
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

> [!NOTE]
> <span data-ttu-id="931e6-295"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> to `null`.</span><span class="sxs-lookup"><span data-stu-id="931e6-295"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="931e6-296">Wstrzyknięcie usług do walidacji w `IsValid` metodzie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="931e6-296">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="931e6-297">Niestandardowe atrybuty klasy walidacji</span><span class="sxs-lookup"><span data-stu-id="931e6-297">Custom validation class attributes</span></span>

<span data-ttu-id="931e6-298">Niestandardowe nazwy klas walidacji są przydatne podczas integrowania z strukturami CSS, takimi jak [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="931e6-298">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="931e6-299">Aby określić niestandardowe nazwy klas walidacji, Utwórz klasę pochodną `FieldCssClassProvider` i ustaw klasę dla tego <xref:Microsoft.AspNetCore.Components.Forms.EditContext> wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="931e6-299">To specify custom validation class names, create a class derived from `FieldCssClassProvider` and set the class on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="931e6-300">Blazor Pakiet weryfikacji adnotacji danych</span><span class="sxs-lookup"><span data-stu-id="931e6-300">Blazor data annotations validation package</span></span>

<span data-ttu-id="931e6-301">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)To pakiet, który wypełnia luki w środowisku walidacji za pomocą <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="931e6-301">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="931e6-302">Pakiet jest obecnie *eksperymentalny*.</span><span class="sxs-lookup"><span data-stu-id="931e6-302">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="931e6-303">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Pakiet ma najnowszą wersję *release candidate* w [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). W tej chwili Kontynuuj korzystanie z pakietu wersji *eksperymentalnej* kandydata.</span><span class="sxs-lookup"><span data-stu-id="931e6-303">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="931e6-304">Zestaw pakietu może zostać przeniesiony do struktury lub środowiska uruchomieniowego w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="931e6-304">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="931e6-305">Obejrzyj repozytorium [anonsów](https://github.com/aspnet/Announcements)w witrynie GitHub, [repozytorium usługi GitHub/aspnetcore](https://github.com/dotnet/aspnetcore)w serwisie w ramach programu lub dalsze aktualizacje.</span><span class="sxs-lookup"><span data-stu-id="931e6-305">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="931e6-306">[CompareProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="931e6-306">[CompareProperty] attribute</span></span>

<span data-ttu-id="931e6-307"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nie działa dobrze ze <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnikiem, ponieważ nie kojarzy wyniku walidacji z określonym elementem członkowskim.</span><span class="sxs-lookup"><span data-stu-id="931e6-307">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="931e6-308">Może to spowodować niespójne zachowanie między walidacją na poziomie pola i po sprawdzeniu poprawności całego modelu podczas przesyłania.</span><span class="sxs-lookup"><span data-stu-id="931e6-308">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="931e6-309">Pakiet [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *eksperymentalny* wprowadza dodatkowy atrybut sprawdzania poprawności, `ComparePropertyAttribute` który działa wokół tych ograniczeń.</span><span class="sxs-lookup"><span data-stu-id="931e6-309">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="931e6-310">W Blazor aplikacji, `[CompareProperty]` jest bezpośrednią wymianą dla [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="931e6-310">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="931e6-311">Modele zagnieżdżone, typy kolekcji i typy złożone</span><span class="sxs-lookup"><span data-stu-id="931e6-311">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="931e6-312">Blazor zapewnia obsługę sprawdzania poprawności formularza przy użyciu adnotacji danych z wbudowaną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="931e6-312">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="931e6-313">Jednak program <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> sprawdza poprawność właściwości najwyższego poziomu modelu powiązanego z formularzem, który nie jest właściwościami typu Collection-lub złożonego.</span><span class="sxs-lookup"><span data-stu-id="931e6-313">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="931e6-314">Aby sprawdzić poprawność całego grafu obiektów modelu powiązanego, w tym właściwości kolekcji i typu złożonego, użyj `ObjectGraphDataAnnotationsValidator` dostarczonej przez pakiet *eksperymentalny* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="931e6-314">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="931e6-315">Dodawanie adnotacji do właściwości modelu za pomocą `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="931e6-315">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="931e6-316">W poniższych klasach modelu `ShipDescription` Klasa zawiera dodatkowe adnotacje danych do zweryfikowania, gdy model jest powiązany z formularzem:</span><span class="sxs-lookup"><span data-stu-id="931e6-316">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="931e6-317">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="931e6-317">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

<span data-ttu-id="931e6-318">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="931e6-318">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="931e6-319">Włączanie przycisku przesyłania na podstawie walidacji formularza</span><span class="sxs-lookup"><span data-stu-id="931e6-319">Enable the submit button based on form validation</span></span>

<span data-ttu-id="931e6-320">Aby włączyć i wyłączyć przycisk Prześlij na podstawie walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="931e6-320">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="931e6-321">Użyj formularza, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Aby przypisać model, gdy składnik zostanie zainicjowany.</span><span class="sxs-lookup"><span data-stu-id="931e6-321">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="931e6-322">Sprawdź poprawność formularza w <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> wywołaniu zwrotnym kontekstu, aby włączyć i wyłączyć przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="931e6-322">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="931e6-323">Odpinanie programu obsługi zdarzeń w `Dispose` metodzie.</span><span class="sxs-lookup"><span data-stu-id="931e6-323">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="931e6-324">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="931e6-324">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="931e6-325">W przypadku korzystania z <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , nie przypisuj również <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="931e6-325">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
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

<span data-ttu-id="931e6-326">W poprzednim przykładzie ustaw wartość `formInvalid` `false` :</span><span class="sxs-lookup"><span data-stu-id="931e6-326">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="931e6-327">Formularz jest wstępnie załadowany z prawidłowymi wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="931e6-327">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="931e6-328">Przycisk Prześlij ma być włączony podczas ładowania formularza.</span><span class="sxs-lookup"><span data-stu-id="931e6-328">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="931e6-329">Efektem ubocznym poprzedniego podejścia jest to, że <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jest wypełniany przy użyciu nieprawidłowych pól, gdy użytkownik przeprowadzi interakcję z jednym polem.</span><span class="sxs-lookup"><span data-stu-id="931e6-329">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="931e6-330">Ten scenariusz można rozwiązać w jeden z następujących sposobów:</span><span class="sxs-lookup"><span data-stu-id="931e6-330">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="931e6-331">Nie używaj <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnika w formularzu.</span><span class="sxs-lookup"><span data-stu-id="931e6-331">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="931e6-332">Ustaw <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jako widoczny po wybraniu przycisku Prześlij (na przykład w `HandleValidSubmit` metodzie).</span><span class="sxs-lookup"><span data-stu-id="931e6-332">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
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

## <a name="troubleshoot"></a><span data-ttu-id="931e6-333">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="931e6-333">Troubleshoot</span></span>

> <span data-ttu-id="931e6-334">InvalidOperationException: EditForm wymaga parametru modelu lub parametru EditContext, ale nie obu.</span><span class="sxs-lookup"><span data-stu-id="931e6-334">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="931e6-335">Upewnij się, że <xref:Microsoft.AspNetCore.Components.Forms.EditForm> element ma wartość <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **lub** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="931e6-335">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="931e6-336">Nie używaj obu tych wartości dla tego samego formularza.</span><span class="sxs-lookup"><span data-stu-id="931e6-336">Don't use both for the same form.</span></span>

<span data-ttu-id="931e6-337">Podczas przypisywania <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do formularza upewnij się, że typ modelu jest skonkretyzowany, jak pokazano na poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="931e6-337">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="931e6-338">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="931e6-338">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end
