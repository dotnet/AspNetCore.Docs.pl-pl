---
title: ASP.NET Core Blazor formularzy i walidacji
author: guardrex
description: Dowiedz się, jak używać scenariuszy i walidacji pól w programie Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/18/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: b485a62c61d404a91134f49cf2a49134ec9f5123
ms.sourcegitcommit: 8ed9a413bdc2d665ad11add8828898d726ccb106
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280390"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>ASP.NET Core Blazor formularzy i walidacji

[Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)i [Luke Latham](https://github.com/guardrex)

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

W powyższym przykładzie:

* Formularz sprawdza poprawność danych wejściowych użytkownika w `name` polu przy użyciu walidacji zdefiniowanej w `ExampleModel` typie. Model jest tworzony w `@code` bloku składnika i przechowywany w prywatnym polu ( `exampleModel` ). Pole jest przypisane do `Model` atrybutu `<EditForm>` elementu.
* <xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Powiązania składnika:
  * Właściwość modelu ( `exampleModel.Name` ) do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` właściwości składnika. Aby uzyskać więcej informacji na temat powiązania właściwości, zobacz <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> .
  * Delegowanie zdarzenia zmiany do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` właściwości składnika.
* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [Składnik walidacji](#validator-components) dołącza obsługę walidacji przy użyciu adnotacji danych.
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje komunikaty weryfikacyjne.
* `HandleValidSubmit` jest wyzwalany po pomyślnym przesłaniu formularza (kończy walidację).

## <a name="built-in-forms-components"></a>Wbudowane składniki formularzy

Zestaw wbudowanych składników jest dostępny do odbierania i weryfikowania danych wejściowych użytkownika. Dane wejściowe są weryfikowane po ich zmianie i po przesłaniu formularza. W poniższej tabeli przedstawiono dostępne składniki danych wejściowych.

::: moniker range=">= aspnetcore-5.0"

| Składnik wejściowy | Renderowane jako&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| Składnik wejściowy | Renderowane jako&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

Wszystkie składniki danych wejściowych, w tym <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , obsługują dowolne atrybuty. Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do renderowanego elementu HTML.

Składniki wejściowe zapewniają domyślne zachowanie podczas sprawdzania poprawności, gdy pole jest zmieniane, włącznie z aktualizacją klasy CSS pola w celu odzwierciedlenia stanu pola. Niektóre składniki obejmują przydatne logiki analizy. Na przykład i bezproblemowo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> obsługiwać wartości nieanalizowane przez rejestrowanie wartości, które nie są analizowane jako błędy walidacji. Typy, które mogą akceptować wartości null, obsługują również wartość null pola docelowego (na przykład `int?` ).

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

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>Tworzy <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [wartość kaskadową](xref:blazor/components/cascading-values-and-parameters) , która śledzi metadane dotyczące procesu edycji, w tym pola, które zostały zmodyfikowane i bieżące komunikaty weryfikacyjne.

Przypisz **albo** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Components.Forms.EditForm> . Przypisanie obu nie jest obsługiwane i generuje **błąd czasu wykonania**.

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>Zapewnia wygodne zdarzenia dla prawidłowego i nieprawidłowego przesłania formularza:

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

Użyj <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> , aby użyć niestandardowego kodu do wyzwalania walidacji i sprawdzania wartości pól.

W poniższym przykładzie:

* `HandleSubmit`Metoda jest wykonywana, gdy **`Submit`** przycisk jest zaznaczony.
* Formularz jest weryfikowany przez wywołanie <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .
* Dodatkowy kod jest wykonywany w zależności od wyniku walidacji. Umieść logikę biznesową w metodzie przypisanej do <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

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
> Interfejs API struktury nie istnieje, aby wyczyścić komunikaty sprawdzania poprawności bezpośrednio z programu <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . W związku z tym zazwyczaj nie zaleca się dodawania komunikatów weryfikacyjnych do nowego <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> w formularzu. Aby zarządzać komunikatami sprawdzania poprawności, należy użyć [składnika modułu sprawdzania](#validator-components) [poprawności kodu weryfikacyjnego logiki biznesowej](#business-logic-validation), zgodnie z opisem w tym artykule.

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a>Obsługa nazw wyświetlanych

*Ta sekcja dotyczy programu .NET 5 Release Candidate 1 (RC1) lub nowszego, który zostanie zwolniony w połowie września.*

Następujące wbudowane składniki obsługują nazwy wyświetlane z `DisplayName` parametrem:

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

W poniższym `InputDate` przykładzie składnika:

* Nazwa wyświetlana ( `DisplayName` ) jest ustawiona na `birthday` .
* Składnik jest powiązany z `BirthDate` właściwością jako `DateTime` typem.

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

Jeśli użytkownik nie poda wartości typu Date, komunikat o błędzie walidacji zostanie wyświetlony w następujący sposób:

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a>Składniki modułu sprawdzania poprawności

Składnik modułu sprawdzania poprawności obsługuje walidację formularza przez zarządzanie <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> dla formularza <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .

BlazorStruktura zapewnia <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik umożliwiający dołączenie obsługi walidacji do formularzy na podstawie [atrybutów walidacji (adnotacje danych)](xref:mvc/models/validation#validation-attributes). Utwórz niestandardowe składniki modułu sprawdzania poprawności, aby przetwarzać komunikaty weryfikacyjne dla różnych formularzy na tej samej stronie lub w tym samym formularzu w różnych krokach przetwarzania formularzy, na przykład Walidacja po stronie klienta, a następnie weryfikacja po stronie serwera. Przykład składnika modułu sprawdzania poprawności przedstawiony w tej sekcji `CustomValidator` jest używany w następujących sekcjach tego artykułu:

* [Sprawdzanie poprawności logiki biznesowej](#business-logic-validation)
* [Sprawdzanie poprawności serwera](#server-validation)

> [!NOTE]
> Atrybuty walidacji niestandardowych adnotacji danych mogą być używane zamiast niestandardowych składników modułu sprawdzania poprawności w wielu przypadkach. Atrybuty niestandardowe zastosowane do modelu formularza aktywują się przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika. Gdy jest używany z walidacją po stronie serwera, wszystkie atrybuty niestandardowe zastosowane do modelu muszą być wykonywalne na serwerze. Aby uzyskać więcej informacji, zobacz <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

Utwórz składnik modułu sprawdzania poprawności z <xref:Microsoft.AspNetCore.Components.ComponentBase> :

* Formularz <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jest [parametrem kaskadowym](xref:blazor/components/cascading-values-and-parameters) składnika.
* Po zainicjowaniu składnika modułu sprawdzania poprawności zostanie <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> utworzony nowy, aby zachować bieżącą listę błędów.
* Magazyn komunikatów otrzymuje błędy, gdy kod dewelopera w składniku formularza wywołuje `DisplayErrors` metodę. Błędy są przesyłane do `DisplayErrors` metody w [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) . W słowniku klucz jest nazwą pola formularza, które zawiera co najmniej jeden błąd. Wartość jest listą błędów.
* Komunikaty są czyszczone w przypadku wystąpienia któregokolwiek z następujących elementów:
  * <xref:Microsoft.AspNetCore.Components.Forms.EditContext>Gdy <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> zdarzenie zostanie zgłoszone, zażądano walidacji. Wszystkie błędy są wyczyszczone.
  * Pole zostanie zmienione w formularzu, gdy <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> zdarzenie zostanie zgłoszone. Tylko błędy dla pola są wyczyszczone.
  * `ClearErrors`Metoda jest wywoływana przez kod dewelopera. Wszystkie błędy są wyczyszczone.

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

## <a name="business-logic-validation"></a>Sprawdzanie poprawności logiki biznesowej

Walidacja logiki biznesowej można wykonać za pomocą [składnika modułu sprawdzania](#validator-components) poprawności, który odbiera błędy formularza w słowniku.

W poniższym przykładzie:

* `CustomValidator`Składnik składnika [walidacji](#validator-components) w tym artykule jest używany.
* Walidacja wymaga wartości dla opisu wysyłki ( `Description` ), jeśli użytkownik wybierze `Defense` klasyfikację statku ( `Classification` ).

Gdy w składniku są ustawiane komunikaty weryfikacji, są one dodawane do modułu sprawdzania poprawności <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> i wyświetlane w <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :

```csharp
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
> Jako alternatywę dla korzystania ze [składników walidacji](#validator-components), można użyć atrybutów walidacji adnotacji danych. Atrybuty niestandardowe zastosowane do modelu formularza aktywują się przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika. Gdy jest używany z walidacją po stronie serwera, atrybuty muszą być wykonywalne na serwerze. Aby uzyskać więcej informacji, zobacz <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

## <a name="server-validation"></a>Sprawdzanie poprawności serwera

Sprawdzanie poprawności serwera można wykonać za pomocą [składnika modułu sprawdzania poprawności](#validator-components)serwera:

* Przetwarzaj sprawdzanie poprawności po stronie klienta w formie ze <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnikiem.
* Gdy formularz przejdzie do walidacji po stronie klienta ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> jest wywoływana), Wyślij <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> do interfejsu API serwera wewnętrznej bazy danych na potrzeby przetwarzania formularza.
* Sprawdzanie poprawności modelu procesu na serwerze.
* Interfejs API serwera zawiera zarówno wbudowaną, jak i niestandardową logikę sprawdzania poprawności. Jeśli walidacja kończy się na serwerze, należy przetworzyć formularz i ponownie wysłać kod stanu sukcesu (*200-OK*). Jeśli walidacja nie powiedzie się, zwróć kod stanu błędu (*400-złe żądanie*) i błędy walidacji pola.
* Wyłącz formularz po powodzeniu lub Wyświetl błędy.

Poniższy przykład jest oparty na:

* Rozwiązanie hostowane Blazor utworzone przez [ Blazor szablon hostowanego projektu](xref:blazor/hosting-models#blazor-webassembly). Przykład może być używany z dowolnym bezpiecznymi Blazor rozwiązaniami obsługiwanymi opisanymi w temacie [zabezpieczenia i Identity Dokumentacja](xref:blazor/security/webassembly/index#implementation-guidance).
* Przykładowa *baza danych Starfleet Starship* w poprzedniej sekcji [wbudowanych elementów formularzy](#built-in-forms-components) .
* Blazor <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Składnik struktury.
* `CustomValidator`Składnik widoczny w sekcji [składniki modułu sprawdzania poprawności](#validator-components) .

W poniższym przykładzie interfejs API serwera sprawdza, czy wartość jest podana dla opisu odbiorcy ( `Description` ), jeśli użytkownik wybierze `Defense` klasyfikację statku ( `Classification` ).

Umieść `Starship` model w projekcie rozwiązania, `Shared` tak aby aplikacje klienta i serwera mogły korzystać z modelu. Ponieważ model wymaga adnotacji danych, Dodaj odwołanie do pakietu dla [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) `Shared` pliku projektu projektu:

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

Aby określić najnowszą wersję pakietu, która nie jest w wersji zapoznawczej, zobacz **historię wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).

W projekcie interfejsu API serwera Dodaj kontroler, aby przetwarzać żądania weryfikacji Starship ( `Controllers/StarshipValidation.cs` ) i zwracać nieudane komunikaty weryfikacji:

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
                logger.LogError("Validation Error: {MESSAGE}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

Gdy na serwerze wystąpi błąd walidacji powiązania modelu, obiekt [`ApiController`](xref:web-api/index) ( <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ) zwykle zwraca domyślną nieprawidłową [odpowiedź na żądanie](xref:web-api/index#default-badrequest-response) z <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> . Odpowiedź zawiera więcej danych niż tylko błędy walidacji, jak pokazano w poniższym przykładzie, gdy nie przesłano wszystkich pól formularza *bazy danych Starfleet Starship* i niepowodzenie walidacji formularza:

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

Jeśli interfejs API serwera zwróci poprzednią domyślną odpowiedź JSON, możliwe jest, aby klient przeanalizować odpowiedź w celu uzyskania elementów podrzędnych `errors` węzła. Nie jest to jednak wygodne do analizy pliku. Analizowanie pliku JSON wymaga dodatkowego kodu po wywołaniu <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> w celu wygenerowania [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) błędów podczas przetwarzania błędów walidacji formularzy. W idealnym przypadku interfejs API serwera powinien zwrócić tylko błędy walidacji:

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

Aby zmodyfikować odpowiedź interfejsu API serwera tak, aby zwracała błędy walidacji, należy zmienić delegata, który jest wywoływany w akcjach, które są adnotacją <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> w programie `Startup.ConfigureServices` . Dla punktu końcowego interfejsu API ( `/StarshipValidation` ) Zwróć wartość <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> . W przypadku innych punktów końcowych interfejsu API Zachowaj zachowanie domyślne, zwracając wynik obiektu z nowym <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :

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

Aby uzyskać więcej informacji, zobacz <xref:web-api/handle-errors#validation-failure-error-response>.

W projekcie klienta Dodaj składnik walidatora widoczny w sekcji [składniki modułu sprawdzania poprawności](#validator-components) .

W projekcie klienta formularz *bazy danych Starfleet Starship* został zaktualizowany w celu wyświetlenia błędów walidacji serwera i pomocy `CustomValidator` składnika. Gdy interfejs API serwera zwraca komunikaty weryfikacyjne, są one dodawane do `CustomValidator` składnika <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> . Błędy są dostępne w formularzu <xref:Microsoft.AspNetCore.Components.Forms.EditContext> na potrzeby wyświetlania według formularza <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :

```csharp
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
            Logger.LogError("Form processing error: {MESSAGE}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> Jako alternatywę dla [składników walidacji](#validator-components), można użyć atrybutów walidacji adnotacji danych. Atrybuty niestandardowe zastosowane do modelu formularza aktywują się przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika. Gdy jest używany z walidacją po stronie serwera, atrybuty muszą być wykonywalne na serwerze. Aby uzyskać więcej informacji, zobacz <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

> [!NOTE]
> Podejście do walidacji po stronie serwera w tej sekcji jest odpowiednie dla dowolnego z Blazor WebAssembly przykładów obsługiwanych rozwiązań w tym zestawie dokumentacji:
>
> * [Usługa Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Identity Server](xref:blazor/security/webassembly/hosted-with-identity-server)

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

## <a name="radio-buttons"></a>Przyciski radiowe

::: moniker range=">= aspnetcore-5.0"

Użyj `InputRadio` składników ze składnikiem, `InputRadioGroup` Aby utworzyć grupę przycisków radiowych. W poniższym przykładzie właściwości są dodawane do `Starship` modelu opisanego w sekcji [wbudowane składniki formularzy](#built-in-forms-components) :

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

Dodaj następujące elementy `enums` do aplikacji. Utwórz nowy plik do przechowywania `enums` lub Dodaj `enums` do `Starship.cs` pliku. Utwórz `enums` dostęp do `Starship` modelu i formularz *bazy danych Starfleet Starship* :

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, Virgin, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

Aktualizowanie formularza *bazy danych Starfleet Starship* opisanej w sekcji [wbudowane składniki formularzy](#built-in-forms-components) . Dodaj składniki do produkcji:

* Grupa przycisków radiowych dla producenta statku.
* Zagnieżdżona grupa przycisków radiowych dla kolorów i aparatu.

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
> Jeśli `Name` parametr zostanie pominięty, `InputRadio` składniki są pogrupowane według ostatniego elementu nadrzędnego.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

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

## <a name="binding-select-element-options-to-c-object-null-values"></a>`<select>`Opcje elementu powiązania z wartościami obiektów C# `null`

Nie istnieje rozsądny sposób reprezentowania `<select>` wartości opcji elementu jako wartości obiektu języka C# `null` , ponieważ:

* Atrybuty HTML nie mogą mieć `null` wartości. Najbliższy odpowiednik `null` w języku HTML to brak `value` atrybutu HTML z `<option>` elementu.
* W przypadku wybrania `<option>` bez `value` atrybutu przeglądarka traktuje wartość jako *zawartość tekstową* `<option>` elementu.

BlazorPlatforma nie próbuje pominąć zachowania domyślnego, ponieważ spowodowałoby to:

* Tworzenie łańcucha obejść specjalnych przypadków w strukturze.
* Istotne zmiany w bieżącym zachowaniu struktury.

::: moniker range=">= aspnetcore-5.0"

Najbardziej wiarygodny `null` odpowiednik w kodzie HTML jest *ciągiem pustym* `value` . BlazorPlatforma obsługuje `null` konwersje do pustych ciągów dla dwukierunkowego powiązania z `<select>` wartością.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

BlazorStruktura nie jest automatycznie obsługiwana `null` dla pustych konwersji ciągów podczas próby dwukierunkowego powiązania z `<select>` wartością. Aby uzyskać więcej informacji, zobacz temat [Usuwanie powiązania `<select>` do wartości null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).

::: moniker-end

## <a name="validation-support"></a>Obsługa walidacji

<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Składnik dołącza obsługę walidacji przy użyciu adnotacji danych do kaskadowo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Włączenie obsługi walidacji przy użyciu adnotacji danych wymaga tego jawnego gestu. Aby użyć innego systemu sprawdzania poprawności niż adnotacje danych, Zastąp zmienną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> implementacją niestandardową. Implementacja ASP.NET Core jest dostępna do inspekcji w źródle referencyjnym: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) . Powyższe linki do źródła odniesienia zawierają kod z `master` gałęzi repozytorium, który reprezentuje bieżące programowanie jednostki produktu dla następnej wersji ASP.NET Core. Aby wybrać gałąź dla innej wersji, użyj selektora gałęzi GitHub (na przykład `release/3.1` ).

Blazor wykonuje dwa typy walidacji:

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

Kontroluj styl komunikatów weryfikacyjnych w arkuszu stylów aplikacji ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ). Klasa domyślna `validation-message` Ustawia kolor tekstu komunikatów walidacji na czerwony:

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>Niestandardowe atrybuty walidacji

Aby upewnić się, że wynik walidacji jest prawidłowo skojarzony z polem przy użyciu [niestandardowego atrybutu walidacji](xref:mvc/models/validation#custom-attributes), należy przekazać kontekst walidacji <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> podczas tworzenia <xref:System.ComponentModel.DataAnnotations.ValidationResult> :

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
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> to `null`. Wstrzyknięcie usług do walidacji w `IsValid` metodzie nie jest obsługiwane.

### <a name="no-locblazor-data-annotations-validation-package"></a>Blazor Pakiet weryfikacji adnotacji danych

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)To pakiet, który wypełnia luki w środowisku walidacji za pomocą <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika. Pakiet jest obecnie *eksperymentalny*.

> [!NOTE]
> [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Pakiet ma najnowszą wersję *release candidate* w [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). W tej chwili Kontynuuj korzystanie z pakietu wersji *eksperymentalnej* kandydata. Zestaw pakietu może zostać przeniesiony do struktury lub środowiska uruchomieniowego w przyszłej wersji. Obejrzyj repozytorium [anonsów](https://github.com/aspnet/Announcements)w witrynie GitHub, [repozytorium usługi GitHub/aspnetcore](https://github.com/dotnet/aspnetcore)w serwisie w ramach programu lub dalsze aktualizacje.

### <a name="compareproperty-attribute"></a>[CompareProperty] — atrybut

<xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nie działa dobrze ze <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnikiem, ponieważ nie kojarzy wyniku walidacji z określonym elementem członkowskim. Może to spowodować niespójne zachowanie między walidacją na poziomie pola i po sprawdzeniu poprawności całego modelu podczas przesyłania. Pakiet [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *eksperymentalny* wprowadza dodatkowy atrybut sprawdzania poprawności, `ComparePropertyAttribute` który działa wokół tych ograniczeń. W Blazor aplikacji, `[CompareProperty]` jest bezpośrednią wymianą dla [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atrybutu.

### <a name="nested-models-collection-types-and-complex-types"></a>Modele zagnieżdżone, typy kolekcji i typy złożone

Blazor zapewnia obsługę sprawdzania poprawności formularza przy użyciu adnotacji danych z wbudowaną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Jednak program <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> sprawdza poprawność właściwości najwyższego poziomu modelu powiązanego z formularzem, który nie jest właściwościami typu Collection-lub złożonego.

Aby sprawdzić poprawność całego grafu obiektów modelu powiązanego, w tym właściwości kolekcji i typu złożonego, użyj `ObjectGraphDataAnnotationsValidator` dostarczonej przez pakiet *eksperymentalny* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
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
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

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

> [!NOTE]
> W przypadku korzystania z <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , nie przypisuj również <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .

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

W poprzednim przykładzie ustaw wartość `formInvalid` `false` :

* Formularz jest wstępnie załadowany z prawidłowymi wartościami domyślnymi.
* Przycisk Prześlij ma być włączony podczas ładowania formularza.

Efektem ubocznym poprzedniego podejścia jest to, że <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jest wypełniany przy użyciu nieprawidłowych pól, gdy użytkownik przeprowadzi interakcję z jednym polem. Ten scenariusz można rozwiązać w jeden z następujących sposobów:

* Nie używaj <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnika w formularzu.
* Ustaw <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jako widoczny po wybraniu przycisku Prześlij (na przykład w `HandleValidSubmit` metodzie).

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

## <a name="troubleshoot"></a>Rozwiązywanie problemów

> InvalidOperationException: EditForm wymaga parametru modelu lub parametru EditContext, ale nie obu.

Upewnij się, że <xref:Microsoft.AspNetCore.Components.Forms.EditForm> element ma wartość <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **lub** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Nie używaj obu tych wartości dla tego samego formularza.

Podczas przypisywania <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do formularza upewnij się, że typ modelu jest skonkretyzowany, jak pokazano na poniższym przykładzie:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
