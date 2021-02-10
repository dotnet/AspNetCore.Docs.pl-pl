---
title: ASP.NET Core Blazor formularzy i walidacji
author: guardrex
description: Dowiedz się, jak używać scenariuszy i walidacji pól w programie Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- appsettings.json
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
uid: blazor/forms-validation
ms.openlocfilehash: c0c672167680cbe2490c7e5b6ff028ca1893d5aa
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106963"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="fbf86-103">ASP.NET Core Blazor formularzy i walidacji</span><span class="sxs-lookup"><span data-stu-id="fbf86-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="fbf86-104">[Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fbf86-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fbf86-105">Formularze i walidacje są obsługiwane w przypadku Blazor używania [adnotacji danych](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="fbf86-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="fbf86-106">Następujący `ExampleModel` Typ definiuje logikę walidacji przy użyciu adnotacji danych:</span><span class="sxs-lookup"><span data-stu-id="fbf86-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="fbf86-107">Formularz jest definiowany przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składnika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="fbf86-108">W poniższej formie przedstawiono typowe elementy, składniki i Razor kod:</span><span class="sxs-lookup"><span data-stu-id="fbf86-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="fbf86-109">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fbf86-109">In the preceding example:</span></span>

* <span data-ttu-id="fbf86-110">Formularz sprawdza poprawność danych wejściowych użytkownika w `name` polu przy użyciu walidacji zdefiniowanej w `ExampleModel` typie.</span><span class="sxs-lookup"><span data-stu-id="fbf86-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="fbf86-111">Model jest tworzony w `@code` bloku składnika i przechowywany w prywatnym polu ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="fbf86-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="fbf86-112">Pole jest przypisane do `Model` atrybutu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="fbf86-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Powiązania składnika:</span><span class="sxs-lookup"><span data-stu-id="fbf86-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="fbf86-114">Właściwość modelu ( `exampleModel.Name` ) do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="fbf86-115">Aby uzyskać więcej informacji na temat powiązania właściwości, zobacz <xref:blazor/components/data-binding#binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-115">For more information on property binding, see <xref:blazor/components/data-binding#binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="fbf86-116">Delegowanie zdarzenia zmiany do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="fbf86-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [Składnik walidacji](#validator-components) dołącza obsługę walidacji przy użyciu adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="fbf86-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="fbf86-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje komunikaty weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="fbf86-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="fbf86-119">`HandleValidSubmit` jest wyzwalany po pomyślnym przesłaniu formularza (kończy walidację).</span><span class="sxs-lookup"><span data-stu-id="fbf86-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="fbf86-120">Wbudowane składniki formularzy</span><span class="sxs-lookup"><span data-stu-id="fbf86-120">Built-in forms components</span></span>

<span data-ttu-id="fbf86-121">Zestaw wbudowanych składników jest dostępny do odbierania i weryfikowania danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="fbf86-122">Dane wejściowe są weryfikowane po ich zmianie i po przesłaniu formularza.</span><span class="sxs-lookup"><span data-stu-id="fbf86-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="fbf86-123">W poniższej tabeli przedstawiono dostępne składniki danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="fbf86-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="fbf86-124">Składnik wejściowy</span><span class="sxs-lookup"><span data-stu-id="fbf86-124">Input component</span></span> | <span data-ttu-id="fbf86-125">Renderowane jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="fbf86-125">Rendered as&hellip;</span></span> |
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

| <span data-ttu-id="fbf86-126">Składnik wejściowy</span><span class="sxs-lookup"><span data-stu-id="fbf86-126">Input component</span></span> | <span data-ttu-id="fbf86-127">Renderowane jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="fbf86-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="fbf86-128">`InputRadio`Składniki i `InputRadioGroup` są dostępne w ASP.NET Core 5,0 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="fbf86-128">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="fbf86-129">Aby uzyskać więcej informacji, wybierz 5,0 lub nowszą wersję tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-129">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="fbf86-130">Wszystkie składniki danych wejściowych, w tym <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="fbf86-130">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="fbf86-131">Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do renderowanego elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="fbf86-131">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="fbf86-132">Składniki wejściowe zapewniają domyślne zachowanie podczas sprawdzania poprawności, gdy pole jest zmieniane, włącznie z aktualizacją klasy CSS pola w celu odzwierciedlenia stanu pola.</span><span class="sxs-lookup"><span data-stu-id="fbf86-132">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="fbf86-133">Niektóre składniki obejmują przydatne logiki analizy.</span><span class="sxs-lookup"><span data-stu-id="fbf86-133">Some components include useful parsing logic.</span></span> <span data-ttu-id="fbf86-134">Na przykład i bezproblemowo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> obsługiwać wartości nieanalizowane przez rejestrowanie wartości, które nie są analizowane jako błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="fbf86-134">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="fbf86-135">Typy, które mogą akceptować wartości null, obsługują również wartość null pola docelowego (na przykład `int?` ).</span><span class="sxs-lookup"><span data-stu-id="fbf86-135">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="fbf86-136">Następujący `Starship` Typ definiuje logikę walidacji przy użyciu większego zestawu właściwości i adnotacji danych niż wcześniej `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="fbf86-136">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="fbf86-137">W powyższym przykładzie `Description` jest opcjonalne, ponieważ nie są obecne adnotacje danych.</span><span class="sxs-lookup"><span data-stu-id="fbf86-137">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="fbf86-138">Następujący formularz sprawdza poprawność danych wejściowych użytkownika przy użyciu weryfikacji zdefiniowanej w `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="fbf86-138">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="fbf86-139"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Tworzy <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [wartość kaskadową](xref:blazor/components/cascading-values-and-parameters) , która śledzi metadane dotyczące procesu edycji, w tym pola, które zostały zmodyfikowane i bieżące komunikaty weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="fbf86-139">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="fbf86-140">Przypisz **albo** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>  <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-140">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="fbf86-141">Przypisanie obu nie jest obsługiwane i generuje **błąd czasu wykonania**.</span><span class="sxs-lookup"><span data-stu-id="fbf86-141">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="fbf86-142"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Zapewnia wygodne zdarzenia dla prawidłowego i nieprawidłowego przesłania formularza:</span><span class="sxs-lookup"><span data-stu-id="fbf86-142">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="fbf86-143">Użyj <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> , aby użyć niestandardowego kodu do wyzwalania walidacji i sprawdzania wartości pól.</span><span class="sxs-lookup"><span data-stu-id="fbf86-143">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="fbf86-144">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fbf86-144">In the following example:</span></span>

* <span data-ttu-id="fbf86-145">`HandleSubmit`Metoda jest wykonywana, gdy **`Submit`** przycisk jest zaznaczony.</span><span class="sxs-lookup"><span data-stu-id="fbf86-145">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="fbf86-146">Formularz jest weryfikowany przez wywołanie <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-146">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="fbf86-147">Dodatkowy kod jest wykonywany w zależności od wyniku walidacji.</span><span class="sxs-lookup"><span data-stu-id="fbf86-147">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="fbf86-148">Umieść logikę biznesową w metodzie przypisanej do <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-148">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

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
> <span data-ttu-id="fbf86-149">Interfejs API struktury nie istnieje, aby wyczyścić komunikaty sprawdzania poprawności bezpośrednio z programu <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-149">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="fbf86-150">W związku z tym zazwyczaj nie zaleca się dodawania komunikatów weryfikacyjnych do nowego <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> w formularzu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-150">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="fbf86-151">Aby zarządzać komunikatami sprawdzania poprawności, należy użyć [składnika modułu sprawdzania](#validator-components) [poprawności kodu weryfikacyjnego logiki biznesowej](#business-logic-validation), zgodnie z opisem w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="fbf86-151">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="fbf86-152">Obsługa nazw wyświetlanych</span><span class="sxs-lookup"><span data-stu-id="fbf86-152">Display name support</span></span>

<span data-ttu-id="fbf86-153">*Ta sekcja ma zastosowanie do ASP.NET Core w programie .NET 5 Release Candidate 1 (RC1) lub nowszym.*</span><span class="sxs-lookup"><span data-stu-id="fbf86-153">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="fbf86-154">Następujące wbudowane składniki obsługują nazwy wyświetlane z `DisplayName` parametrem:</span><span class="sxs-lookup"><span data-stu-id="fbf86-154">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="fbf86-155">W poniższym `InputDate` przykładzie składnika:</span><span class="sxs-lookup"><span data-stu-id="fbf86-155">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="fbf86-156">Nazwa wyświetlana ( `DisplayName` ) jest ustawiona na `birthday` .</span><span class="sxs-lookup"><span data-stu-id="fbf86-156">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="fbf86-157">Składnik jest powiązany z `BirthDate` właściwością jako `DateTime` typem.</span><span class="sxs-lookup"><span data-stu-id="fbf86-157">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="fbf86-158">Jeśli użytkownik nie poda wartości typu Date, komunikat o błędzie walidacji zostanie wyświetlony w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="fbf86-158">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="fbf86-159">Składniki modułu sprawdzania poprawności</span><span class="sxs-lookup"><span data-stu-id="fbf86-159">Validator components</span></span>

<span data-ttu-id="fbf86-160">Składnik modułu sprawdzania poprawności obsługuje walidację formularza przez zarządzanie <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> dla formularza <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-160">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="fbf86-161">BlazorStruktura zapewnia <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik umożliwiający dołączenie obsługi walidacji do formularzy na podstawie [atrybutów walidacji (adnotacje danych)](xref:mvc/models/validation#validation-attributes).</span><span class="sxs-lookup"><span data-stu-id="fbf86-161">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="fbf86-162">Utwórz niestandardowe składniki modułu sprawdzania poprawności, aby przetwarzać komunikaty weryfikacyjne dla różnych formularzy na tej samej stronie lub w tym samym formularzu w różnych krokach przetwarzania formularzy, na przykład Walidacja po stronie klienta, a następnie weryfikacja po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="fbf86-162">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="fbf86-163">Przykład składnika modułu sprawdzania poprawności przedstawiony w tej sekcji `CustomValidator` jest używany w następujących sekcjach tego artykułu:</span><span class="sxs-lookup"><span data-stu-id="fbf86-163">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="fbf86-164">Sprawdzanie poprawności logiki biznesowej</span><span class="sxs-lookup"><span data-stu-id="fbf86-164">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="fbf86-165">Sprawdzanie poprawności serwera</span><span class="sxs-lookup"><span data-stu-id="fbf86-165">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="fbf86-166">Atrybuty walidacji niestandardowych adnotacji danych mogą być używane zamiast niestandardowych składników modułu sprawdzania poprawności w wielu przypadkach.</span><span class="sxs-lookup"><span data-stu-id="fbf86-166">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="fbf86-167">Atrybuty niestandardowe zastosowane do modelu formularza aktywują się przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-167">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="fbf86-168">Gdy jest używany z walidacją po stronie serwera, wszystkie atrybuty niestandardowe zastosowane do modelu muszą być wykonywalne na serwerze.</span><span class="sxs-lookup"><span data-stu-id="fbf86-168">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="fbf86-169">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="fbf86-169">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="fbf86-170">Utwórz składnik modułu sprawdzania poprawności z <xref:Microsoft.AspNetCore.Components.ComponentBase> :</span><span class="sxs-lookup"><span data-stu-id="fbf86-170">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="fbf86-171">Formularz <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jest [parametrem kaskadowym](xref:blazor/components/cascading-values-and-parameters) składnika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-171">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="fbf86-172">Po zainicjowaniu składnika modułu sprawdzania poprawności zostanie <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> utworzony nowy, aby zachować bieżącą listę błędów.</span><span class="sxs-lookup"><span data-stu-id="fbf86-172">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="fbf86-173">Magazyn komunikatów otrzymuje błędy, gdy kod dewelopera w składniku formularza wywołuje `DisplayErrors` metodę.</span><span class="sxs-lookup"><span data-stu-id="fbf86-173">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="fbf86-174">Błędy są przesyłane do `DisplayErrors` metody w [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) .</span><span class="sxs-lookup"><span data-stu-id="fbf86-174">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="fbf86-175">W słowniku klucz jest nazwą pola formularza, które zawiera co najmniej jeden błąd.</span><span class="sxs-lookup"><span data-stu-id="fbf86-175">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="fbf86-176">Wartość jest listą błędów.</span><span class="sxs-lookup"><span data-stu-id="fbf86-176">The value is the error list.</span></span>
* <span data-ttu-id="fbf86-177">Komunikaty są czyszczone w przypadku wystąpienia któregokolwiek z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="fbf86-177">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="fbf86-178"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>Gdy <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> zdarzenie zostanie zgłoszone, zażądano walidacji.</span><span class="sxs-lookup"><span data-stu-id="fbf86-178">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="fbf86-179">Wszystkie błędy są wyczyszczone.</span><span class="sxs-lookup"><span data-stu-id="fbf86-179">All of the errors are cleared.</span></span>
  * <span data-ttu-id="fbf86-180">Pole zostanie zmienione w formularzu, gdy <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> zdarzenie zostanie zgłoszone.</span><span class="sxs-lookup"><span data-stu-id="fbf86-180">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="fbf86-181">Tylko błędy dla pola są wyczyszczone.</span><span class="sxs-lookup"><span data-stu-id="fbf86-181">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="fbf86-182">`ClearErrors`Metoda jest wywoływana przez kod dewelopera.</span><span class="sxs-lookup"><span data-stu-id="fbf86-182">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="fbf86-183">Wszystkie błędy są wyczyszczone.</span><span class="sxs-lookup"><span data-stu-id="fbf86-183">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

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
```

> [!NOTE]
> <span data-ttu-id="fbf86-184">Anonimowe wyrażenia lambda są zarejestrowanymi programami obsługi zdarzeń dla <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> i <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="fbf86-184">Anonymous lambda expressions are registered event handlers for <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> and <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> in the preceding example.</span></span> <span data-ttu-id="fbf86-185">Nie trzeba implementować <xref:System.IDisposable> i anulować subskrybowania delegatów zdarzeń w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-185">It isn't necessary to implement <xref:System.IDisposable> and unsubscribe the event delegates in this scenario.</span></span> <span data-ttu-id="fbf86-186">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="fbf86-186">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="business-logic-validation"></a><span data-ttu-id="fbf86-187">Sprawdzanie poprawności logiki biznesowej</span><span class="sxs-lookup"><span data-stu-id="fbf86-187">Business logic validation</span></span>

<span data-ttu-id="fbf86-188">Walidacja logiki biznesowej można wykonać za pomocą [składnika modułu sprawdzania](#validator-components) poprawności, który odbiera błędy formularza w słowniku.</span><span class="sxs-lookup"><span data-stu-id="fbf86-188">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="fbf86-189">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fbf86-189">In the following example:</span></span>

* <span data-ttu-id="fbf86-190">`CustomValidator`Składnik składnika [walidacji](#validator-components) w tym artykule jest używany.</span><span class="sxs-lookup"><span data-stu-id="fbf86-190">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="fbf86-191">Walidacja wymaga wartości dla opisu wysyłki ( `Description` ), jeśli użytkownik wybierze `Defense` klasyfikację statku ( `Classification` ).</span><span class="sxs-lookup"><span data-stu-id="fbf86-191">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="fbf86-192">Gdy w składniku są ustawiane komunikaty weryfikacji, są one dodawane do modułu sprawdzania poprawności <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> i wyświetlane w <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :</span><span class="sxs-lookup"><span data-stu-id="fbf86-192">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

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
> <span data-ttu-id="fbf86-193">Jako alternatywę dla korzystania ze [składników walidacji](#validator-components), można użyć atrybutów walidacji adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="fbf86-193">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="fbf86-194">Atrybuty niestandardowe zastosowane do modelu formularza aktywują się przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-194">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="fbf86-195">Gdy jest używany z walidacją po stronie serwera, atrybuty muszą być wykonywalne na serwerze.</span><span class="sxs-lookup"><span data-stu-id="fbf86-195">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="fbf86-196">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="fbf86-196">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="fbf86-197">Sprawdzanie poprawności serwera</span><span class="sxs-lookup"><span data-stu-id="fbf86-197">Server validation</span></span>

<span data-ttu-id="fbf86-198">Sprawdzanie poprawności serwera można wykonać za pomocą [składnika modułu sprawdzania poprawności](#validator-components)serwera:</span><span class="sxs-lookup"><span data-stu-id="fbf86-198">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="fbf86-199">Przetwarzaj sprawdzanie poprawności po stronie klienta w formie ze <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnikiem.</span><span class="sxs-lookup"><span data-stu-id="fbf86-199">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="fbf86-200">Gdy formularz przejdzie do walidacji po stronie klienta ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> jest wywoływana), Wyślij <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> do interfejsu API serwera wewnętrznej bazy danych na potrzeby przetwarzania formularza.</span><span class="sxs-lookup"><span data-stu-id="fbf86-200">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="fbf86-201">Sprawdzanie poprawności modelu procesu na serwerze.</span><span class="sxs-lookup"><span data-stu-id="fbf86-201">Process model validation on the server.</span></span>
* <span data-ttu-id="fbf86-202">Interfejs API serwera zawiera zarówno wbudowaną, jak i niestandardową logikę sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="fbf86-202">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="fbf86-203">Jeśli walidacja kończy się na serwerze, należy przetworzyć formularz i ponownie wysłać kod stanu sukcesu (*200-OK*).</span><span class="sxs-lookup"><span data-stu-id="fbf86-203">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="fbf86-204">Jeśli walidacja nie powiedzie się, zwróć kod stanu błędu (*400-złe żądanie*) i błędy walidacji pola.</span><span class="sxs-lookup"><span data-stu-id="fbf86-204">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="fbf86-205">Wyłącz formularz po powodzeniu lub Wyświetl błędy.</span><span class="sxs-lookup"><span data-stu-id="fbf86-205">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="fbf86-206">Poniższy przykład jest oparty na:</span><span class="sxs-lookup"><span data-stu-id="fbf86-206">The following example is based on:</span></span>

* <span data-ttu-id="fbf86-207">Rozwiązanie hostowane Blazor utworzone przez [ Blazor szablon hostowanego projektu](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="fbf86-207">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="fbf86-208">Przykład może być używany z dowolnym bezpiecznymi Blazor rozwiązaniami obsługiwanymi opisanymi w temacie [zabezpieczenia i Identity Dokumentacja](xref:blazor/security/webassembly/index#implementation-guidance).</span><span class="sxs-lookup"><span data-stu-id="fbf86-208">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="fbf86-209">Przykładowa *baza danych Starfleet Starship* w poprzedniej sekcji [wbudowanych elementów formularzy](#built-in-forms-components) .</span><span class="sxs-lookup"><span data-stu-id="fbf86-209">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="fbf86-210">Blazor <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Składnik struktury.</span><span class="sxs-lookup"><span data-stu-id="fbf86-210">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="fbf86-211">`CustomValidator`Składnik widoczny w sekcji [składniki modułu sprawdzania poprawności](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="fbf86-211">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="fbf86-212">W poniższym przykładzie interfejs API serwera sprawdza, czy wartość jest podana dla opisu odbiorcy ( `Description` ), jeśli użytkownik wybierze `Defense` klasyfikację statku ( `Classification` ).</span><span class="sxs-lookup"><span data-stu-id="fbf86-212">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="fbf86-213">Umieść `Starship` model w projekcie rozwiązania, `Shared` tak aby aplikacje klienta i serwera mogły korzystać z modelu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-213">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="fbf86-214">Ponieważ model wymaga adnotacji danych, Dodaj odwołanie do pakietu dla [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) `Shared` pliku projektu projektu:</span><span class="sxs-lookup"><span data-stu-id="fbf86-214">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="fbf86-215">Aby określić najnowszą wersję pakietu, która nie jest w wersji zapoznawczej, zobacz **historię wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span><span class="sxs-lookup"><span data-stu-id="fbf86-215">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="fbf86-216">W projekcie interfejsu API serwera Dodaj kontroler, aby przetwarzać żądania weryfikacji Starship ( `Controllers/StarshipValidation.cs` ) i zwracać nieudane komunikaty weryfikacji:</span><span class="sxs-lookup"><span data-stu-id="fbf86-216">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace {ASSEMBLY NAME}.Controllers
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

<span data-ttu-id="fbf86-217">W poprzednim przykładzie symbol zastępczy `{ASSEMBLY NAME}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.Server` ).</span><span class="sxs-lookup"><span data-stu-id="fbf86-217">In the preceding example, the placeholder `{ASSEMBLY NAME}` is the app's assembly name (for example, `BlazorSample.Server`).</span></span>

<span data-ttu-id="fbf86-218">Gdy na serwerze wystąpi błąd walidacji powiązania modelu, obiekt [`ApiController`](xref:web-api/index) ( <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ) zwykle zwraca domyślną nieprawidłową [odpowiedź na żądanie](xref:web-api/index#default-badrequest-response) z <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-218">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="fbf86-219">Odpowiedź zawiera więcej danych niż tylko błędy walidacji, jak pokazano w poniższym przykładzie, gdy nie przesłano wszystkich pól formularza *bazy danych Starfleet Starship* i niepowodzenie walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="fbf86-219">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

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

<span data-ttu-id="fbf86-220">Jeśli interfejs API serwera zwróci poprzednią domyślną odpowiedź JSON, możliwe jest, aby klient przeanalizować odpowiedź w celu uzyskania elementów podrzędnych `errors` węzła.</span><span class="sxs-lookup"><span data-stu-id="fbf86-220">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="fbf86-221">Nie jest to jednak wygodne do analizy pliku.</span><span class="sxs-lookup"><span data-stu-id="fbf86-221">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="fbf86-222">Analizowanie pliku JSON wymaga dodatkowego kodu po wywołaniu <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> w celu wygenerowania [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) błędów podczas przetwarzania błędów walidacji formularzy.</span><span class="sxs-lookup"><span data-stu-id="fbf86-222">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="fbf86-223">W idealnym przypadku interfejs API serwera powinien zwrócić tylko błędy walidacji:</span><span class="sxs-lookup"><span data-stu-id="fbf86-223">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="fbf86-224">Aby zmodyfikować odpowiedź interfejsu API serwera tak, aby zwracała błędy walidacji, należy zmienić delegata, który jest wywoływany w akcjach, które są adnotacją <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fbf86-224">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fbf86-225">Dla punktu końcowego interfejsu API ( `/StarshipValidation` ) Zwróć wartość <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-225">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="fbf86-226">W przypadku innych punktów końcowych interfejsu API Zachowaj zachowanie domyślne, zwracając wynik obiektu z nowym <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :</span><span class="sxs-lookup"><span data-stu-id="fbf86-226">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

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

<span data-ttu-id="fbf86-227">Aby uzyskać więcej informacji, zobacz <xref:web-api/handle-errors#validation-failure-error-response>.</span><span class="sxs-lookup"><span data-stu-id="fbf86-227">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="fbf86-228">W projekcie klienta Dodaj składnik walidatora widoczny w sekcji [składniki modułu sprawdzania poprawności](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="fbf86-228">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="fbf86-229">W projekcie klienta formularz *bazy danych Starfleet Starship* został zaktualizowany w celu wyświetlenia błędów walidacji serwera i pomocy `CustomValidator` składnika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-229">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="fbf86-230">Gdy interfejs API serwera zwraca komunikaty weryfikacyjne, są one dodawane do `CustomValidator` składnika <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-230">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="fbf86-231">Błędy są dostępne w formularzu <xref:Microsoft.AspNetCore.Components.Forms.EditContext> na potrzeby wyświetlania według formularza <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :</span><span class="sxs-lookup"><span data-stu-id="fbf86-231">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

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
> <span data-ttu-id="fbf86-232">Jako alternatywę dla [składników walidacji](#validator-components), można użyć atrybutów walidacji adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="fbf86-232">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="fbf86-233">Atrybuty niestandardowe zastosowane do modelu formularza aktywują się przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-233">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="fbf86-234">Gdy jest używany z walidacją po stronie serwera, atrybuty muszą być wykonywalne na serwerze.</span><span class="sxs-lookup"><span data-stu-id="fbf86-234">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="fbf86-235">Aby uzyskać więcej informacji, zobacz <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="fbf86-235">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="fbf86-236">Podejście do walidacji po stronie serwera w tej sekcji jest odpowiednie dla dowolnego z Blazor WebAssembly przykładów obsługiwanych rozwiązań w tym zestawie dokumentacji:</span><span class="sxs-lookup"><span data-stu-id="fbf86-236">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="fbf86-237">Usługa Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="fbf86-237">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="fbf86-238">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="fbf86-238">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="fbf86-239">Identity Server</span><span class="sxs-lookup"><span data-stu-id="fbf86-239">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="fbf86-240">InputText na podstawie zdarzenia wejściowego</span><span class="sxs-lookup"><span data-stu-id="fbf86-240">InputText based on the input event</span></span>

<span data-ttu-id="fbf86-241">Użyj <xref:Microsoft.AspNetCore.Components.Forms.InputText> składnika, aby utworzyć niestandardowy składnik, który używa `input` zdarzenia zamiast `change` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="fbf86-241">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="fbf86-242">W poniższym przykładzie `CustomInputText` składnik dziedziczy `InputText` składnik platformy i ustawia powiązanie zdarzenia ze `oninput` zdarzeniem.</span><span class="sxs-lookup"><span data-stu-id="fbf86-242">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets event binding to the `oninput` event.</span></span>

<span data-ttu-id="fbf86-243">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="fbf86-243">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input @attributes="AdditionalAttributes" class="@CssClass" 
    @bind="CurrentValueAsString" @bind:event="oninput" />
```

<span data-ttu-id="fbf86-244">`CustomInputText`Składnika można używać wszędzie tam, gdzie <xref:Microsoft.AspNetCore.Components.Forms.InputText> są używane:</span><span class="sxs-lookup"><span data-stu-id="fbf86-244">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="fbf86-245">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="fbf86-245">`Pages/TestForm.razor`:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="fbf86-246">Przyciski radiowe</span><span class="sxs-lookup"><span data-stu-id="fbf86-246">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fbf86-247">Użyj `InputRadio` składników ze składnikiem, `InputRadioGroup` Aby utworzyć grupę przycisków radiowych.</span><span class="sxs-lookup"><span data-stu-id="fbf86-247">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="fbf86-248">W poniższym przykładzie właściwości są dodawane do `Starship` modelu opisanego w sekcji [wbudowane składniki formularzy](#built-in-forms-components) :</span><span class="sxs-lookup"><span data-stu-id="fbf86-248">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

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

<span data-ttu-id="fbf86-249">Dodaj następujące elementy `enums` do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fbf86-249">Add the following `enums` to the app.</span></span> <span data-ttu-id="fbf86-250">Utwórz nowy plik do przechowywania `enums` lub Dodaj `enums` do `Starship.cs` pliku.</span><span class="sxs-lookup"><span data-stu-id="fbf86-250">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="fbf86-251">Utwórz `enums` dostęp do `Starship` modelu i formularz *bazy danych Starfleet Starship* :</span><span class="sxs-lookup"><span data-stu-id="fbf86-251">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="fbf86-252">Aktualizowanie formularza *bazy danych Starfleet Starship* opisanej w sekcji [wbudowane składniki formularzy](#built-in-forms-components) .</span><span class="sxs-lookup"><span data-stu-id="fbf86-252">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="fbf86-253">Dodaj składniki do produkcji:</span><span class="sxs-lookup"><span data-stu-id="fbf86-253">Add the components to produce:</span></span>

* <span data-ttu-id="fbf86-254">Grupa przycisków radiowych dla producenta statku.</span><span class="sxs-lookup"><span data-stu-id="fbf86-254">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="fbf86-255">Zagnieżdżona grupa przycisków radiowych dla kolorów i aparatu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-255">A nested radio button group for ship color and engine.</span></span>

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
> <span data-ttu-id="fbf86-256">Jeśli `Name` parametr zostanie pominięty, `InputRadio` składniki są pogrupowane według ostatniego elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="fbf86-256">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="fbf86-257">Podczas pracy z przyciskami radiowymi w formularzu powiązanie danych jest obsługiwane inaczej niż inne elementy, ponieważ przyciski radiowe są oceniane jako Grupa.</span><span class="sxs-lookup"><span data-stu-id="fbf86-257">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="fbf86-258">Wartość każdego przycisku radiowego jest stała, ale wartość grupy przycisków radiowych jest wartością wybranego przycisku radiowego.</span><span class="sxs-lookup"><span data-stu-id="fbf86-258">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="fbf86-259">Poniższy przykład pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="fbf86-259">The following example shows how to:</span></span>

* <span data-ttu-id="fbf86-260">Obsługa powiązania danych dla grupy przycisków radiowych.</span><span class="sxs-lookup"><span data-stu-id="fbf86-260">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="fbf86-261">Obsługa walidacji przy użyciu `InputRadio` składnika niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="fbf86-261">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="fbf86-262">Poniższe <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składniki używają powyższego `InputRadio` składnika do uzyskania i zweryfikowania klasyfikacji od użytkownika:</span><span class="sxs-lookup"><span data-stu-id="fbf86-262">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="fbf86-263">`<select>`Opcje elementu powiązania z wartościami obiektów C# `null`</span><span class="sxs-lookup"><span data-stu-id="fbf86-263">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="fbf86-264">Nie istnieje rozsądny sposób reprezentowania `<select>` wartości opcji elementu jako wartości obiektu języka C# `null` , ponieważ:</span><span class="sxs-lookup"><span data-stu-id="fbf86-264">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="fbf86-265">Atrybuty HTML nie mogą mieć `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="fbf86-265">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="fbf86-266">Najbliższy odpowiednik `null` w języku HTML to brak `value` atrybutu HTML z `<option>` elementu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-266">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="fbf86-267">W przypadku wybrania `<option>` bez `value` atrybutu przeglądarka traktuje wartość jako *zawartość tekstową* `<option>` elementu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-267">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="fbf86-268">BlazorPlatforma nie próbuje pominąć zachowania domyślnego, ponieważ spowodowałoby to:</span><span class="sxs-lookup"><span data-stu-id="fbf86-268">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="fbf86-269">Tworzenie łańcucha obejść specjalnych przypadków w strukturze.</span><span class="sxs-lookup"><span data-stu-id="fbf86-269">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="fbf86-270">Istotne zmiany w bieżącym zachowaniu struktury.</span><span class="sxs-lookup"><span data-stu-id="fbf86-270">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fbf86-271">Najbardziej wiarygodny `null` odpowiednik w kodzie HTML jest *ciągiem pustym* `value` .</span><span class="sxs-lookup"><span data-stu-id="fbf86-271">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="fbf86-272">BlazorPlatforma obsługuje `null` konwersje do pustych ciągów dla dwukierunkowego powiązania z `<select>` wartością.</span><span class="sxs-lookup"><span data-stu-id="fbf86-272">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="fbf86-273">BlazorStruktura nie jest automatycznie obsługiwana `null` dla pustych konwersji ciągów podczas próby dwukierunkowego powiązania z `<select>` wartością.</span><span class="sxs-lookup"><span data-stu-id="fbf86-273">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="fbf86-274">Aby uzyskać więcej informacji, zobacz temat [Usuwanie powiązania `<select>` do wartości null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="fbf86-274">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="fbf86-275">Obsługa walidacji</span><span class="sxs-lookup"><span data-stu-id="fbf86-275">Validation support</span></span>

<span data-ttu-id="fbf86-276"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Składnik dołącza obsługę walidacji przy użyciu adnotacji danych do kaskadowo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-276">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="fbf86-277">Włączenie obsługi walidacji przy użyciu adnotacji danych wymaga tego jawnego gestu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-277">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="fbf86-278">Aby użyć innego systemu sprawdzania poprawności niż adnotacje danych, Zastąp zmienną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> implementacją niestandardową.</span><span class="sxs-lookup"><span data-stu-id="fbf86-278">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="fbf86-279">Implementacja ASP.NET Core jest dostępna do inspekcji w źródle referencyjnym: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="fbf86-279">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="fbf86-280">Powyższe linki do źródła odniesienia zawierają kod z `master` gałęzi repozytorium, który reprezentuje bieżące programowanie jednostki produktu dla następnej wersji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fbf86-280">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="fbf86-281">Aby wybrać gałąź dla innej wersji, użyj selektora gałęzi GitHub (na przykład `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="fbf86-281">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="fbf86-282">Blazor wykonuje dwa typy walidacji:</span><span class="sxs-lookup"><span data-stu-id="fbf86-282">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="fbf86-283">*Sprawdzanie poprawności pola* jest wykonywane, gdy użytkownik wyprowadzi karty z pola.</span><span class="sxs-lookup"><span data-stu-id="fbf86-283">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="fbf86-284">Podczas sprawdzania poprawności pola <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik kojarzy wszystkie zgłoszone wyniki walidacji z polem.</span><span class="sxs-lookup"><span data-stu-id="fbf86-284">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="fbf86-285">*Sprawdzanie poprawności modelu* jest wykonywane, gdy użytkownik prześle formularz.</span><span class="sxs-lookup"><span data-stu-id="fbf86-285">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="fbf86-286">Podczas walidacji modelu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik próbuje określić pole na podstawie nazwy elementu członkowskiego, który raportuje wynik sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="fbf86-286">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="fbf86-287">Wyniki walidacji, które nie są skojarzone z poszczególnymi elementami członkowskimi, są skojarzone z modelem, a nie polem.</span><span class="sxs-lookup"><span data-stu-id="fbf86-287">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="fbf86-288">Składniki podsumowania walidacji i komunikatów weryfikacji</span><span class="sxs-lookup"><span data-stu-id="fbf86-288">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="fbf86-289"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje wszystkie komunikaty weryfikacyjne podobne do [pomocnika tagów podsumowania weryfikacji](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="fbf86-289">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="fbf86-290">Wyprowadź komunikaty weryfikacji dla określonego modelu z `Model` parametrem:</span><span class="sxs-lookup"><span data-stu-id="fbf86-290">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="fbf86-291"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składnik wyświetla komunikaty sprawdzania poprawności dla określonego pola, które jest podobne do [pomocnika tagów komunikatu weryfikacji](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="fbf86-291">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="fbf86-292">Określ pole do walidacji z `For` atrybutem i wyrażeniem lambda, które nazywa właściwość modelu:</span><span class="sxs-lookup"><span data-stu-id="fbf86-292">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="fbf86-293"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składniki i <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="fbf86-293">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="fbf86-294">Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do wygenerowanego `<div>` `<ul>` elementu or.</span><span class="sxs-lookup"><span data-stu-id="fbf86-294">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="fbf86-295">Kontroluj styl komunikatów weryfikacyjnych w arkuszu stylów aplikacji ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ).</span><span class="sxs-lookup"><span data-stu-id="fbf86-295">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="fbf86-296">Klasa domyślna `validation-message` Ustawia kolor tekstu komunikatów walidacji na czerwony:</span><span class="sxs-lookup"><span data-stu-id="fbf86-296">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="fbf86-297">Niestandardowe atrybuty walidacji</span><span class="sxs-lookup"><span data-stu-id="fbf86-297">Custom validation attributes</span></span>

<span data-ttu-id="fbf86-298">Aby upewnić się, że wynik walidacji jest prawidłowo skojarzony z polem przy użyciu [niestandardowego atrybutu walidacji](xref:mvc/models/validation#custom-attributes), należy przekazać kontekst walidacji <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> podczas tworzenia <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="fbf86-298">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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
> <span data-ttu-id="fbf86-299"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> to `null`.</span><span class="sxs-lookup"><span data-stu-id="fbf86-299"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="fbf86-300">Wstrzyknięcie usług do walidacji w `IsValid` metodzie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="fbf86-300">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="fbf86-301">Niestandardowe atrybuty klasy walidacji</span><span class="sxs-lookup"><span data-stu-id="fbf86-301">Custom validation class attributes</span></span>

<span data-ttu-id="fbf86-302">Niestandardowe nazwy klas walidacji są przydatne podczas integrowania z strukturami CSS, takimi jak [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="fbf86-302">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="fbf86-303">Aby określić niestandardowe nazwy klas walidacji, Utwórz klasę pochodną `FieldCssClassProvider` i ustaw klasę dla tego <xref:Microsoft.AspNetCore.Components.Forms.EditContext> wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="fbf86-303">To specify custom validation class names, create a class derived from `FieldCssClassProvider` and set the class on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a><span data-ttu-id="fbf86-304">Blazor Pakiet weryfikacji adnotacji danych</span><span class="sxs-lookup"><span data-stu-id="fbf86-304">Blazor data annotations validation package</span></span>

<span data-ttu-id="fbf86-305">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)To pakiet, który wypełnia luki w środowisku walidacji za pomocą <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="fbf86-305">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="fbf86-306">Pakiet jest obecnie *eksperymentalny*.</span><span class="sxs-lookup"><span data-stu-id="fbf86-306">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="fbf86-307">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Pakiet ma najnowszą wersję *release candidate* w [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). W tej chwili Kontynuuj korzystanie z pakietu wersji *eksperymentalnej* kandydata.</span><span class="sxs-lookup"><span data-stu-id="fbf86-307">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="fbf86-308">Zestaw pakietu może zostać przeniesiony do struktury lub środowiska uruchomieniowego w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="fbf86-308">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="fbf86-309">Obejrzyj repozytorium [anonsów](https://github.com/aspnet/Announcements)w witrynie GitHub, [repozytorium usługi GitHub/aspnetcore](https://github.com/dotnet/aspnetcore)w serwisie w ramach programu lub dalsze aktualizacje.</span><span class="sxs-lookup"><span data-stu-id="fbf86-309">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a><span data-ttu-id="fbf86-310">Atrybut `[CompareProperty]`</span><span class="sxs-lookup"><span data-stu-id="fbf86-310">`[CompareProperty]` attribute</span></span>

<span data-ttu-id="fbf86-311"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nie działa dobrze ze <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnikiem, ponieważ nie kojarzy wyniku walidacji z określonym elementem członkowskim.</span><span class="sxs-lookup"><span data-stu-id="fbf86-311">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="fbf86-312">Może to spowodować niespójne zachowanie między walidacją na poziomie pola i po sprawdzeniu poprawności całego modelu podczas przesyłania.</span><span class="sxs-lookup"><span data-stu-id="fbf86-312">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="fbf86-313">Pakiet [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *eksperymentalny* wprowadza dodatkowy atrybut sprawdzania poprawności, `ComparePropertyAttribute` który działa wokół tych ograniczeń.</span><span class="sxs-lookup"><span data-stu-id="fbf86-313">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="fbf86-314">W Blazor aplikacji, `[CompareProperty]` jest bezpośrednią wymianą dla [ `[Compare]` atrybutu](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span><span class="sxs-lookup"><span data-stu-id="fbf86-314">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]` attribute](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span></span>

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="fbf86-315">Modele zagnieżdżone, typy kolekcji i typy złożone</span><span class="sxs-lookup"><span data-stu-id="fbf86-315">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="fbf86-316">Blazor zapewnia obsługę sprawdzania poprawności formularza przy użyciu adnotacji danych z wbudowaną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-316">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="fbf86-317">Jednak program <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> sprawdza poprawność właściwości najwyższego poziomu modelu powiązanego z formularzem, który nie jest właściwościami typu Collection-lub złożonego.</span><span class="sxs-lookup"><span data-stu-id="fbf86-317">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="fbf86-318">Aby sprawdzić poprawność całego grafu obiektów modelu powiązanego, w tym właściwości kolekcji i typu złożonego, użyj `ObjectGraphDataAnnotationsValidator` dostarczonej przez pakiet *eksperymentalny* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="fbf86-318">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="fbf86-319">Dodawanie adnotacji do właściwości modelu za pomocą `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="fbf86-319">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="fbf86-320">W poniższych klasach modelu `ShipDescription` Klasa zawiera dodatkowe adnotacje danych do zweryfikowania, gdy model jest powiązany z formularzem:</span><span class="sxs-lookup"><span data-stu-id="fbf86-320">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="fbf86-321">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="fbf86-321">`Starship.cs`:</span></span>

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

<span data-ttu-id="fbf86-322">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="fbf86-322">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="fbf86-323">Włączanie przycisku przesyłania na podstawie walidacji formularza</span><span class="sxs-lookup"><span data-stu-id="fbf86-323">Enable the submit button based on form validation</span></span>

<span data-ttu-id="fbf86-324">Aby włączyć i wyłączyć przycisk Prześlij na podstawie walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="fbf86-324">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="fbf86-325">Użyj formularza, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Aby przypisać model, gdy składnik zostanie zainicjowany.</span><span class="sxs-lookup"><span data-stu-id="fbf86-325">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="fbf86-326">Sprawdź poprawność formularza w <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> wywołaniu zwrotnym kontekstu, aby włączyć i wyłączyć przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="fbf86-326">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="fbf86-327">Zaimplementuj <xref:System.IDisposable> i Anuluj subskrypcję programu obsługi zdarzeń w `Dispose` metodzie.</span><span class="sxs-lookup"><span data-stu-id="fbf86-327">Implement <xref:System.IDisposable> and unsubscribe the event handler in the `Dispose` method.</span></span> <span data-ttu-id="fbf86-328">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="fbf86-328">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="fbf86-329">W przypadku korzystania z <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , nie przypisuj również <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-329">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

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

<span data-ttu-id="fbf86-330">W poprzednim przykładzie ustaw wartość `formInvalid` `false` :</span><span class="sxs-lookup"><span data-stu-id="fbf86-330">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="fbf86-331">Formularz jest wstępnie załadowany z prawidłowymi wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="fbf86-331">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="fbf86-332">Przycisk Prześlij ma być włączony podczas ładowania formularza.</span><span class="sxs-lookup"><span data-stu-id="fbf86-332">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="fbf86-333">Efektem ubocznym poprzedniego podejścia jest to, że <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jest wypełniany przy użyciu nieprawidłowych pól, gdy użytkownik przeprowadzi interakcję z jednym polem.</span><span class="sxs-lookup"><span data-stu-id="fbf86-333">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="fbf86-334">Ten scenariusz można rozwiązać w jeden z następujących sposobów:</span><span class="sxs-lookup"><span data-stu-id="fbf86-334">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="fbf86-335">Nie używaj <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnika w formularzu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-335">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="fbf86-336">Ustaw <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jako widoczny po wybraniu przycisku Prześlij (na przykład w `HandleValidSubmit` metodzie).</span><span class="sxs-lookup"><span data-stu-id="fbf86-336">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="fbf86-337">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="fbf86-337">Troubleshoot</span></span>

> <span data-ttu-id="fbf86-338">InvalidOperationException: EditForm wymaga parametru modelu lub parametru EditContext, ale nie obu.</span><span class="sxs-lookup"><span data-stu-id="fbf86-338">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="fbf86-339">Upewnij się, że <xref:Microsoft.AspNetCore.Components.Forms.EditForm> element ma wartość <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **lub** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="fbf86-339">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="fbf86-340">Nie używaj obu tych wartości dla tego samego formularza.</span><span class="sxs-lookup"><span data-stu-id="fbf86-340">Don't use both for the same form.</span></span>

<span data-ttu-id="fbf86-341">Podczas przypisywania <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do formularza upewnij się, że typ modelu jest skonkretyzowany, jak pokazano na poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fbf86-341">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="fbf86-342">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="fbf86-342">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end
