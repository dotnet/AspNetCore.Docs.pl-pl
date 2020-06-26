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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 1ed87b4aa2519334d2339b500a615aa96ef4d57d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402965"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor formularzy i walidacji

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

Formularze i walidacje są obsługiwane w przypadku Blazor używania [adnotacji danych](xref:mvc/models/validation).

Następujący `ExampleModel` Typ definiuje logikę walidacji przy użyciu adnotacji danych:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Formularz jest definiowany przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składnika. W poniższej formie przedstawiono typowe elementy, składniki i Razor kod:

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

W poprzednim przykładzie:

* Formularz sprawdza poprawność danych wejściowych użytkownika w `name` polu przy użyciu walidacji zdefiniowanej w `ExampleModel` typie. Model jest tworzony w `@code` bloku składnika i przechowywany w prywatnym polu ( `exampleModel` ). Pole jest przypisane do `Model` atrybutu `<EditForm>` elementu.
* <xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Powiązania składnika:
  * Właściwość modelu ( `exampleModel.Name` ) do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` właściwości składnika. Aby uzyskać więcej informacji na temat powiązania właściwości, zobacz <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> .
  * Delegowanie zdarzenia zmiany do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` właściwości składnika.
* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Składnik dołącza obsługę walidacji przy użyciu adnotacji danych.
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje komunikaty weryfikacyjne.
* `HandleValidSubmit`jest wyzwalany po pomyślnym przesłaniu formularza (kończy walidację).

Zestaw wbudowanych składników wejściowych jest dostępny do odbierania i weryfikowania danych wejściowych użytkownika. Dane wejściowe są weryfikowane po ich zmianie i po przesłaniu formularza. W poniższej tabeli przedstawiono dostępne składniki danych wejściowych.

| Składnik wejściowy | Renderowane jako&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

Wszystkie składniki danych wejściowych, w tym <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , obsługują dowolne atrybuty. Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do renderowanego elementu HTML.

Składniki wejściowe zapewniają domyślne zachowanie podczas sprawdzania poprawności edycji i zmiany ich klasy CSS, aby odzwierciedlały stan pola. Niektóre składniki obejmują przydatne logiki analizy. Na przykład i bezproblemowo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> obsłużyć wartości, które można przeanalizować, rejestrując je jako błędy walidacji. Typy, które mogą akceptować wartości null, obsługują również wartość null pola docelowego (na przykład `int?` ).

Następujący `Starship` Typ definiuje logikę walidacji przy użyciu większego zestawu właściwości i adnotacji danych niż wcześniej `ExampleModel` :

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

W powyższym przykładzie `Description` jest opcjonalne, ponieważ nie są obecne adnotacje danych.

Następujący formularz sprawdza poprawność danych wejściowych użytkownika przy użyciu weryfikacji zdefiniowanej w `Starship` modelu:

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

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>Tworzy <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [wartość kaskadową](xref:blazor/components/cascading-values-and-parameters) , która śledzi metadane dotyczące procesu edycji, w tym pola, które zostały zmodyfikowane i bieżące komunikaty weryfikacyjne. <xref:Microsoft.AspNetCore.Components.Forms.EditForm>Zapewnia również wygodne zdarzenia dla prawidłowych i nieprawidłowych przesyłania ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ). Alternatywnie można użyć <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> do wyzwolenia walidacji i sprawdzenia wartości pól z niestandardowym kodem walidacji.

W poniższym przykładzie:

* `HandleSubmit`Metoda jest uruchamiana po **`Submit`** wybraniu przycisku.
* Formularz zostanie sprawdzony przy użyciu formularza <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
* Formularz jest weryfikowany przez przekazanie <xref:Microsoft.AspNetCore.Components.Forms.EditContext> do `ServerValidate` metody, która wywołuje punkt końcowy interfejsu API sieci Web na serwerze (*niepokazywany*).
* Dodatkowy kod jest uruchamiany w zależności od wyniku sprawdzenia poprawności po stronie klienta i serwera `isValid` .

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

## <a name="inputtext-based-on-the-input-event"></a>InputText na podstawie zdarzenia wejściowego

Użyj <xref:Microsoft.AspNetCore.Components.Forms.InputText> składnika, aby utworzyć niestandardowy składnik, który używa `input` zdarzenia zamiast `change` zdarzenia.

W poniższym przykładzie `CustomInputText` składnik dziedziczy `InputText` składnik platformy i ustawia powiązanie zdarzenia ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) na `oninput` zdarzenie.

`Shared/CustomInputText.razor`:

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

`CustomInputText`Składnika można używać wszędzie tam, gdzie <xref:Microsoft.AspNetCore.Components.Forms.InputText> są używane:

`Pages/TestForm.razor`:

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

## <a name="work-with-radio-buttons"></a>Pracuj z przyciskami radiowymi

Podczas pracy z przyciskami radiowymi w formularzu powiązanie danych jest obsługiwane inaczej niż inne elementy, ponieważ przyciski radiowe są oceniane jako Grupa. Wartość każdego przycisku radiowego jest stała, ale wartość grupy przycisków radiowych jest wartością wybranego przycisku radiowego. Poniższy przykład pokazuje, jak:

* Obsługa powiązania danych dla grupy przycisków radiowych.
* Obsługa walidacji przy użyciu `InputRadio` składnika niestandardowego.

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

Poniższe <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składniki używają powyższego `InputRadio` składnika do uzyskania i zweryfikowania klasyfikacji od użytkownika:

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

## <a name="validation-support"></a>Obsługa walidacji

<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Składnik dołącza obsługę walidacji przy użyciu adnotacji danych do kaskadowo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Włączenie obsługi walidacji przy użyciu adnotacji danych wymaga tego jawnego gestu. Aby użyć innego systemu sprawdzania poprawności niż adnotacje danych, Zastąp zmienną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> implementacją niestandardową. Implementacja ASP.NET Core jest dostępna do inspekcji w źródle referencyjnym: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) . Powyższe linki do źródła odniesienia zawierają kod z `master` gałęzi repozytorium, który reprezentuje bieżące programowanie jednostki produktu dla następnej wersji ASP.NET Core. Aby wybrać gałąź dla innej wersji, użyj selektora gałęzi GitHub (na przykład `release/3.1` ).

Blazorwykonuje dwa typy walidacji:

* *Sprawdzanie poprawności pola* jest wykonywane, gdy użytkownik wyprowadzi karty z pola. Podczas sprawdzania poprawności pola <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik kojarzy wszystkie zgłoszone wyniki walidacji z polem.
* *Sprawdzanie poprawności modelu* jest wykonywane, gdy użytkownik prześle formularz. Podczas walidacji modelu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik próbuje określić pole na podstawie nazwy elementu członkowskiego, który raportuje wynik sprawdzania poprawności. Wyniki walidacji, które nie są skojarzone z poszczególnymi elementami członkowskimi, są skojarzone z modelem, a nie polem.

### <a name="validation-summary-and-validation-message-components"></a>Składniki podsumowania walidacji i komunikatów weryfikacji

<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje wszystkie komunikaty weryfikacyjne podobne do [pomocnika tagów podsumowania weryfikacji](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Wyprowadź komunikaty weryfikacji dla określonego modelu z `Model` parametrem:
  
```razor
<ValidationSummary Model="@starship" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składnik wyświetla komunikaty sprawdzania poprawności dla określonego pola, które jest podobne do [pomocnika tagów komunikatu weryfikacji](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Określ pole do walidacji z `For` atrybutem i wyrażeniem lambda, które nazywa właściwość modelu:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składniki i <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> obsługują dowolne atrybuty. Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do wygenerowanego `<div>` `<ul>` elementu or.

### <a name="custom-validation-attributes"></a>Niestandardowe atrybuty walidacji

Aby upewnić się, że wynik walidacji jest prawidłowo skojarzony z polem przy użyciu [niestandardowego atrybutu walidacji](xref:mvc/models/validation#custom-attributes), należy przekazać kontekst walidacji <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> podczas tworzenia <xref:System.ComponentModel.DataAnnotations.ValidationResult> :

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

### <a name="blazor-data-annotations-validation-package"></a>BlazorPakiet weryfikacji adnotacji danych

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)To pakiet, który wypełnia luki w środowisku walidacji za pomocą <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika. Pakiet jest obecnie *eksperymentalny*.

### <a name="compareproperty-attribute"></a>[CompareProperty] — atrybut

<xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nie działa dobrze ze <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnikiem, ponieważ nie kojarzy wyniku walidacji z określonym elementem członkowskim. Może to spowodować niespójne zachowanie między walidacją na poziomie pola i po sprawdzeniu poprawności całego modelu podczas przesyłania. Pakiet [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *eksperymentalny* wprowadza dodatkowy atrybut sprawdzania poprawności, `ComparePropertyAttribute` który działa wokół tych ograniczeń. W Blazor aplikacji, `[CompareProperty]` jest bezpośrednią wymianą dla [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atrybutu.

### <a name="nested-models-collection-types-and-complex-types"></a>Modele zagnieżdżone, typy kolekcji i typy złożone

Blazorzapewnia obsługę sprawdzania poprawności formularza przy użyciu adnotacji danych z wbudowaną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Jednak program <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> sprawdza poprawność właściwości najwyższego poziomu modelu powiązanego z formularzem, który nie jest właściwościami typu Collection-lub złożonego.

Aby sprawdzić poprawność całego grafu obiektów modelu powiązanego, w tym właściwości kolekcji i typu złożonego, użyj `ObjectGraphDataAnnotationsValidator` dostarczonej przez pakiet *eksperymentalny* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Dodawanie adnotacji do właściwości modelu za pomocą `[ValidateComplexType]` . W poniższych klasach modelu `ShipDescription` Klasa zawiera dodatkowe adnotacje danych do zweryfikowania, gdy model jest powiązany z formularzem:

`Starship.cs`:

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

`ShipDescription.cs`:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Włączanie przycisku przesyłania na podstawie walidacji formularza

Aby włączyć i wyłączyć przycisk Prześlij na podstawie walidacji formularza:

* Użyj formularza, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Aby przypisać model, gdy składnik zostanie zainicjowany.
* Sprawdź poprawność formularza w <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> wywołaniu zwrotnym kontekstu, aby włączyć i wyłączyć przycisk Prześlij.
* Odpinanie programu obsługi zdarzeń w `Dispose` metodzie. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

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

W poprzednim przykładzie ustaw wartość `formInvalid` `false` :

* Formularz jest wstępnie załadowany z prawidłowymi wartościami domyślnymi.
* Przycisk Prześlij ma być włączony podczas ładowania formularza.

Efektem ubocznym poprzedniego podejścia jest to, że <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jest wypełniany przy użyciu nieprawidłowych pól, gdy użytkownik przeprowadzi interakcję z jednym polem. Ten scenariusz można rozwiązać w jeden z następujących sposobów:

* Nie używaj <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnika w formularzu.
* Ustaw <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jako widoczny po wybraniu przycisku Prześlij (na przykład w `HandleValidSubmit` metodzie).

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

## <a name="troubleshoot"></a>Rozwiązywanie problemów

> InvalidOperationException: EditForm wymaga parametru modelu lub parametru EditContext, ale nie obu.

Upewnij się, że <xref:Microsoft.AspNetCore.Components.Forms.EditForm> element ma wartość <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> lub <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .

Podczas przypisywania <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do formularza upewnij się, że typ modelu jest skonkretyzowany, jak pokazano na poniższym przykładzie:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
