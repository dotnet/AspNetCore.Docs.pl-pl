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
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET podstawowe formularze Blazora i walidacja

Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)

Formularze i sprawdzanie poprawności są obsługiwane w blazorze przy użyciu [adnotacji danych](xref:mvc/models/validation).

Następujący `ExampleModel` typ definiuje logikę sprawdzania poprawności przy użyciu adnotacji danych:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Formularz jest definiowany `EditForm` za pomocą komponentu. Poniższy formularz pokazuje typowe elementy, składniki i kod Razor:

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

W poprzednim przykładzie:

* Formularz sprawdza poprawność danych `name` wejściowych użytkownika w `ExampleModel` polu przy użyciu sprawdzania poprawności zdefiniowanej w typie. Model jest tworzony w `@code` bloku komponentu i utrzymywany`_exampleModel`w prywatnym polu ( ). Pole jest przypisane `Model` do atrybutu `<EditForm>` elementu.
* `@bind-Value` Wiąże `InputText` składnik:
  * Właściwość modelu`_exampleModel.Name`( `InputText` ) do `Value` właściwości składnika.
  * Delegat zdarzenia zmiany `InputText` do właściwości `ValueChanged` składnika.
* Składnik `DataAnnotationsValidator` dołącza obsługę sprawdzania poprawności przy użyciu adnotacji danych.
* Składnik `ValidationSummary` podsumowuje komunikaty sprawdzania poprawności.
* `HandleValidSubmit`jest wyzwalany, gdy formularz zostanie pomyślnie przesłany (przechodzi weryfikację).

Zestaw wbudowanych składników wejściowych są dostępne do odbierania i sprawdzania poprawności danych wejściowych użytkownika. Dane wejściowe są sprawdzane po ich zmianie i przesłaniu formularza. Dostępne składniki wejściowe przedstawiono w poniższej tabeli.

| Składnik wejściowy | Renderowane jako&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Wszystkie składniki wejściowe, `EditForm`w tym , obsługują dowolne atrybuty. Każdy atrybut, który nie pasuje do parametru składnika, jest dodawany do renderowanego elementu HTML.

Składniki wejściowe zapewniają domyślne zachowanie sprawdzania poprawności podczas edytowania i zmieniania klasy CSS w celu odzwierciedlenia stanu pola. Niektóre składniki zawierają logikę analizowania przydatne. Na przykład `InputDate` `InputNumber` i obsługiwać wartości niepardzielalne bezpiecznie rejestrując je jako błędy sprawdzania poprawności. Typy, które mogą akceptować wartości null, obsługują `int?`również nieważność pola docelowego (na przykład ).

Następujący `Starship` typ definiuje logikę sprawdzania poprawności przy użyciu większego zestawu właściwości `ExampleModel`i adnotacji danych niż wcześniej:

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

W poprzednim przykładzie `Description` jest opcjonalne, ponieważ nie są obecne adnotacje danych.

Następujący formularz sprawdza poprawność danych wejściowych `Starship` użytkownika przy użyciu sprawdzania poprawności zdefiniowanej w modelu:

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

Tworzy `EditForm` `EditContext` jako [wartość kaskadową,](xref:blazor/components#cascading-values-and-parameters) która śledzi metadane o procesie edycji, w tym, które pola zostały zmodyfikowane i bieżące komunikaty sprawdzania poprawności. Zapewnia `EditForm` również wygodne zdarzenia dla prawidłowych`OnValidSubmit` `OnInvalidSubmit`i nieprawidłowych przesłań ( , ). Alternatywnie można `OnSubmit` użyć do wyzwolenia sprawdzania poprawności i sprawdzić wartości pól za pomocą niestandardowego kodu sprawdzania poprawności.

W poniższym przykładzie:

* Metoda `HandleSubmit` jest uruchamiana po wybraniu przycisku **Prześlij.**
* Formularz jest sprawdzany przy użyciu `EditContext`formularza .
* Formularz jest dalej weryfikowany przez przekazanie `EditContext` do `ServerValidate` metody, która wywołuje punkt końcowy interfejsu API sieci web na serwerze ( nie*pokazano*).
* Dodatkowy kod jest uruchamiany w zależności od wyniku sprawdzania poprawności `isValid`po stronie klienta i serwera przez sprawdzenie.

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

## <a name="inputtext-based-on-the-input-event"></a>InputText na podstawie zdarzenia wejściowego

Składnik `InputText` służy do tworzenia składnika niestandardowego, który używa `input` zdarzenia zamiast `change` zdarzenia.

Utwórz komponent z następującymi znacznikami i `InputText` użyj składnika tak, jak jest używany:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Praca z przyciskami radiowymi

Podczas pracy z przyciskami radiowymi w formularzu powiązanie danych jest obsługiwane inaczej niż inne elementy, ponieważ przyciski radiowe są oceniane jako grupa. Wartość każdego przycisku radiowego jest stała, ale wartość grupy przycisków opcji jest wartością wybranego przycisku radiowego. W poniższym przykładzie pokazano, jak:

* Obsługa powiązania danych dla grupy przycisków opcji.
* Obsługa sprawdzania poprawności `InputRadio` przy użyciu składnika niestandardowego.

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

Poniższy `EditForm` element używa `InputRadio` poprzedniego składnika do uzyskania i sprawdzenia poprawności oceny od użytkownika:

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

## <a name="validation-support"></a>Obsługa sprawdzania poprawności

Składnik `DataAnnotationsValidator` dołącza obsługę sprawdzania poprawności przy użyciu adnotacji danych do kaskadowego `EditContext`. Włączenie obsługi sprawdzania poprawności przy użyciu adnotacji danych wymaga tego jawnego gestu. Aby użyć innego systemu sprawdzania poprawności niż `DataAnnotationsValidator` adnotacje danych, zastąp implementacją niestandardową. Implementacja ASP.NET Core jest dostępna do inspekcji w źródle referencyjnym: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazorwykonuje dwa typy walidacji:

* *Sprawdzanie poprawności pola* jest wykonywane, gdy użytkownik wyjmuje z pola. Podczas sprawdzania poprawności `DataAnnotationsValidator` pola składnik kojarzy wszystkie zgłoszone wyniki sprawdzania poprawności z tym polem.
* *Sprawdzanie poprawności modelu* jest wykonywane, gdy użytkownik przesyła formularz. Podczas sprawdzania poprawności `DataAnnotationsValidator` modelu składnik próbuje określić pole na podstawie nazwy elementu członkowskiego, który raportuje wynik sprawdzania poprawności. Wyniki sprawdzania poprawności, które nie są skojarzone z poszczególnych elementów członkowskich są skojarzone z modelu, a nie pola.

### <a name="validation-summary-and-validation-message-components"></a>Podsumowanie sprawdzania poprawności i sprawdzanie poprawności składników komunikatu

Składnik `ValidationSummary` podsumowuje wszystkie komunikaty sprawdzania poprawności, które są podobne do [Pomocnika znacznika podsumowania sprawdzania poprawności:](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)

```razor
<ValidationSummary />
```

Komunikaty sprawdzania poprawności danych `Model` wyjściowych dla określonego modelu z parametrem:
  
```razor
<ValidationSummary Model="@_starship" />
```

Składnik `ValidationMessage` wyświetla komunikaty sprawdzania poprawności dla określonego pola, które jest podobne do [Pomocnika tagu wiadomości sprawdzania poprawności](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Określ pole sprawdzania `For` poprawności za pomocą atrybutu i wyrażenia lambda nazewnictwa właściwości modelu:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

Składniki `ValidationMessage` `ValidationSummary` i obsługują dowolne atrybuty. Każdy atrybut, który nie pasuje do parametru `<div>` składnika jest dodawany do wygenerowanego lub `<ul>` elementu.

### <a name="custom-validation-attributes"></a>Niestandardowe atrybuty sprawdzania poprawności

Aby upewnić się, że wynik sprawdzania poprawności jest poprawnie skojarzony z polem <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> podczas korzystania <xref:System.ComponentModel.DataAnnotations.ValidationResult>z [niestandardowego atrybutu sprawdzania poprawności,](xref:mvc/models/validation#custom-attributes)przekaż kontekst sprawdzania poprawności podczas tworzenia:

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazorpakiet sprawdzania poprawności adnotacji danych

[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) to pakiet, który wypełnia luki w `DataAnnotationsValidator` zabezpieczeniach za pomocą składnika. Pakiet jest obecnie *eksperymentalny*.

### <a name="compareproperty-attribute"></a>Atrybut [CompareProperty]

Nie <xref:System.ComponentModel.DataAnnotations.CompareAttribute> działa dobrze ze `DataAnnotationsValidator` składnikiem, ponieważ nie kojarzy wynik sprawdzania poprawności z określonym elementem członkowskim. Może to spowodować niespójne zachowanie między sprawdzanie poprawności na poziomie pola i po weryfikacji całego modelu na przesyłanie. Pakiet *eksperymentalny* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) wprowadza dodatkowy atrybut `ComparePropertyAttribute`sprawdzania poprawności, który działa wokół tych ograniczeń. W Blazor aplikacji `[CompareProperty]` jest bezpośrednim zamiennikiem `[Compare]` atrybutu.

### <a name="nested-models-collection-types-and-complex-types"></a>Modele zagnieżdżone, typy kolekcji i typy złożone

Blazorzapewnia obsługę sprawdzania poprawności danych wejściowych przy użyciu `DataAnnotationsValidator`adnotacji danych z wbudowanym programem . Jednak `DataAnnotationsValidator` tylko sprawdza poprawność właściwości najwyższego poziomu modelu powiązanego z formularzem, które nie są właściwościami typu kolekcji lub kompleksu.

Aby sprawdzić poprawność całego wykresu obiektu modelu powiązanego, w tym właściwości `ObjectGraphDataAnnotationsValidator` typu kolekcji i kompleksu, należy użyć dostarczonego przez *eksperymentalny* pakiet [Microsoft.AspNetCore.Components.DataAnnotations.Validation:](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Dodawanie adnotacji do `[ValidateComplexType]`właściwości modelu za pomocą pliku . W następujących klasach `ShipDescription` modelu klasa zawiera dodatkowe adnotacje danych do sprawdzania poprawności, gdy model jest powiązany z formularzem:

*Starship.cs:*

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

*ShipDescription.cs:*

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Włącz przycisk przesyłania na podstawie sprawdzania poprawności formularza

Aby włączyć i wyłączyć przycisk przesyłania na podstawie sprawdzania poprawności formularza:

* Formularz służy `EditContext` do przypisywania modelu podczas inicjowania komponentu.
* Sprawdź poprawność formularza w `OnFieldChanged` wywołaniu zwrotnym kontekstu, aby włączyć i wyłączyć przycisk przesyłania.
* Odłącz program obsługi zdarzeń `Dispose` w metodzie. Aby uzyskać więcej informacji, zobacz <xref:blazor/lifecycle#component-disposal-with-idisposable>.

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

W poprzednim przykładzie `_formInvalid` ustaw, czy: `false`

* Formularz jest wstępnie załadowany z prawidłowymi wartościami domyślnymi.
* Przycisk prześlij ma być włączony po załadowaniu formularza.

Efektem ubocznym poprzedniego podejścia jest `ValidationSummary` to, że składnik jest wypełniany nieprawidłowymi polami po interakcji użytkownika z dowolnym jednym polem. Ten scenariusz można rozwiązać w jeden z następujących sposobów:

* Nie używaj `ValidationSummary` składnika w formularzu.
* Spraw, `ValidationSummary` aby komponent był widoczny po wybraniu przycisku przesyłania (na przykład w metodzie). `HandleValidSubmit`

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
