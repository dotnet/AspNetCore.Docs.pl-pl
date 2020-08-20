---
title: Pomocnicy tagów w formularzach w ASP.NET Core
author: rick-anderson
description: Opisuje wbudowane pomocnicy tagów używane z formularzami.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/working-with-forms
ms.openlocfilehash: c337e727a4683b0b3c67307af93ef8efa246e2ad
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631020"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>Pomocnicy tagów w formularzach w ASP.NET Core

Autor [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette)i [Jerrie Pelser](https://github.com/jerriep)

Ten dokument przedstawia pracę z formularzami i elementami HTML często używanymi w formularzu. Element HTML [form](https://www.w3.org/TR/html401/interact/forms.html) zawiera podstawowe mechanizmy aplikacje sieci Web używane do publikowania danych na serwerze. W większości tego dokumentu opisano [pomocników tagów](tag-helpers/intro.md) i sposób, w jaki mogą one pomóc w produktywnym tworzeniu niezawodnych formularzy HTML. Zalecamy zapoznanie [się z wprowadzeniem do pomocników tagów](tag-helpers/intro.md) przed przeczytaniem tego dokumentu.

W wielu przypadkach pomocników HTML udostępniają alternatywne podejście do osoby pomagającej z określonym znacznikiem, ale ważne jest, aby rozpoznać, że pomocnicy tagów nie zastępują pomocników HTML i nie ma pomocy dla tagów dla każdego pomocnika HTML. Gdy istnieje alternatywa dla pomocnika HTML, jest ona wspomniana.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>Pomocnik tagów formularza

Pomocnik tagu [formularza](https://www.w3.org/TR/html401/interact/forms.html) :

* Generuje [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` wartość atrybutu HTML dla akcji kontrolera MVC lub nazwanej trasy

* Generuje [token weryfikacji ukrytego żądania](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , aby zapobiec fałszerstwu żądania między lokacjami (gdy jest używany z `[ValidateAntiForgeryToken]` atrybutem w metodzie akcji post protokołu HTTP).

* Udostępnia `asp-route-<Parameter Name>` atrybut, gdzie `<Parameter Name>` jest dodawany do wartości trasy. `routeValues`Parametry do `Html.BeginForm` i `Html.BeginRouteForm` zapewniają podobną funkcjonalność.

* Ma alternatywę pomocnika HTML `Html.BeginForm` i `Html.BeginRouteForm`

Przykład:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

Pomocnik tagu formularza powyżej generuje następujący kod HTML:

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

Środowisko uruchomieniowe MVC generuje `action` wartość atrybutu na podstawie atrybutów pomocnika tagów `asp-controller` i `asp-action` . Pomocnik tagu formularza generuje również [token weryfikacji ukrytego żądania](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , aby zapobiec fałszerstwu żądania między lokacjami (gdy jest używany z `[ValidateAntiForgeryToken]` atrybutem w metodzie akcji post protokołu HTTP). Ochrona czystej formy HTML z poziomu fałszerstwa żądań między lokacjami jest trudna, ponieważ pomocnik tagów formularza udostępnia tę usługę.

### <a name="using-a-named-route"></a>Używanie nazwanej trasy

`asp-route`Atrybut pomocnika tagów może również generować znaczniki dla atrybutu HTML `action` . Aplikacja o [trasie](../../fundamentals/routing.md)  o nazwie `register` może korzystać z następującego znacznika na stronie rejestracji:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Wiele widoków w folderze *widoki/konto* (generowane podczas tworzenia nowej aplikacji sieci Web przy użyciu *poszczególnych kont użytkowników*) zawiera atrybut [ASP-Route-ReturnUrl](xref:mvc/views/working-with-forms) :

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Przy użyciu wbudowanych szablonów `returnUrl` jest wypełniany automatycznie tylko wtedy, gdy użytkownik próbuje uzyskać dostęp do autoryzowanego zasobu, ale nie jest uwierzytelniany ani autoryzowany. W przypadku próby uzyskania nieautoryzowanego dostępu oprogramowanie pośredniczące zabezpieczeń przekierowuje użytkownika do strony logowania z `returnUrl` zestawem.

## <a name="the-form-action-tag-helper"></a>Pomocnik tagów akcji formularza

Pomocnik tagów akcji formularza generuje `formaction` atrybut dla wygenerowanego `<button ...>` tagu or `<input type="image" ...>` . `formaction`Atrybut określa, w którym formularzu są przesyłane dane. Tworzy powiązanie z [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elementami typu `image` i [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elementów. Pomocnik tagów akcji formularza umożliwia użycie kilku atrybutów [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` do kontrolowania tego, jakie `formaction` łącze jest generowane dla odpowiedniego elementu.

Obsługiwane atrybuty [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) do sterowania wartością `formaction` :

|Atrybut|Opis|
|---|---|
|[ASP-Controller](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|Nazwa kontrolera.|
|[ASP — akcja](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|Nazwa metody akcji.|
|[obszar ASP](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|Nazwa obszaru.|
|[ASP — Strona](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|Nazwa Razor strony.|
|[ASP — obsługa stron](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|Nazwa Razor procedury obsługi strony.|
|[ASP — Route](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|Nazwa trasy.|
|[ASP-Route-{Value}](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|Wartość trasy pojedynczego adresu URL. Na przykład `asp-route-id="1234"`.|
|[ASP — wszystkie trasy — dane](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|Wszystkie wartości trasy.|
|[ASP — fragment](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|Fragment adresu URL.|

### <a name="submit-to-controller-example"></a>Przykład przesłania do kontrolera

Następujące znaczniki przesłaniają formularz do `Index` akcji, `HomeController` gdy wybrane jest wejście lub przycisk:

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

Poprzednia Adiustacja generuje następujący kod HTML:

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a>Prześlij do przykładowej strony

Następujący znak przesłania formularz do `About` Razor strony:

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

Poprzednia Adiustacja generuje następujący kod HTML:

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a>Prześlij do przykładu trasy

Rozważ użycie `/Home/Test` punktu końcowego:

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

Poniższe znaczniki przesłaniają formularz do `/Home/Test` punktu końcowego.

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

Poprzednia Adiustacja generuje następujący kod HTML:

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a>Pomocnik tagu wejściowego

Pomocnik tagu wejściowego tworzy powiązanie elementu HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) z wyrażeniem modelu w widoku Razor.

Składnia:

```cshtml
<input asp-for="<Expression Name>">
```

Pomocnik tagu wejściowego:

* Generuje `id` `name` atrybuty HTML dla nazwy wyrażenia określonego w `asp-for` atrybucie. `asp-for="Property1.Property2"` jest odpowiednikiem `m => m.Property1.Property2` . Nazwa wyrażenia jest używana jako `asp-for` wartość atrybutu. Aby uzyskać dodatkowe informacje, zobacz sekcję [nazwy wyrażeń](#expression-names) .

* Ustawia `type` wartość atrybutu HTML na podstawie atrybutów typu modelu i  [adnotacji danych](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) zastosowanych do właściwości model

* Nie zastępuje `type` wartości atrybutu HTML, gdy jest określony

* Generuje atrybuty walidacji [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  z atrybutów [adnotacji danych](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) zastosowanych do właściwości modelu

* Ma funkcję pomocnika HTML, która pokrywa się z `Html.TextBoxFor` i `Html.EditorFor` . Szczegóły można znaleźć w sekcji pomocnik **HTML — alternatywy dla tagów wejściowych** .

* Zapewnia silne wpisywanie. Jeśli nazwa właściwości ulegnie zmianie i nie zostanie zaktualizowany pomocnika tagów, zostanie wyświetlony komunikat o błędzie podobny do następującego:

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

`Input`Pomocnik tagów ustawia ATRYBUT HTML w `type` oparciu o typ .NET. W poniższej tabeli wymieniono niektóre typowe typy .NET i wygenerowany typ HTML (nie każdy typ .NET jest wyświetlany).

|Typ .NET|Typ danych wejściowych|
|---|---|
|Wartość logiczna|Type = "CheckBox"|
|Ciąg|Type = "text"|
|DateTime|Type =["DateTime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Byte|Type = "number"|
|int|Type = "number"|
|Pojedyncza, Podwójna|Type = "number"|

W poniższej tabeli przedstawiono niektóre typowe atrybuty [adnotacji danych](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) , które pomocnik tagów wejściowych będzie mapowany do określonych typów danych wejściowych (nie każdy atrybut walidacji jest wymieniony):

|Atrybut|Typ danych wejściowych|
|---|---|
|EmailAddress|Type = "e-mail"|
|[URL]|Type = "URL"|
|[HiddenInput]|Type = "Hidden"|
|Połączenia|Type = "Tel"|
|[DataType (DataType. Password)]|Type = "Password"|
|[DataType (DataType. Date)]|Type = "date"|
|[DataType (DataType. Time)]|Type = "Time"|

Przykład:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

Kod powyżej generuje następujący HTML:

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

Adnotacje danych zastosowane do `Email` właściwości i `Password` generują metadane w modelu. Pomocnik tagu wejściowego korzysta z metadanych modelu i tworzy atrybuty [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (zobacz [Walidacja modelu](../models/validation.md)). Te atrybuty opisują moduły walidacji do dołączenia do pól wejściowych. Zapewnia to niezauważalne sprawdzanie poprawności HTML5 i [jQuery](https://jquery.com/) . Atrybuty niezauważalne mają format `data-val-rule="Error Message"` , gdzie rule jest nazwą Reguły walidacji (np `data-val-required` ., `data-val-email` `data-val-maxlength` itp.) Jeśli w atrybucie jest podany komunikat o błędzie, jest on wyświetlany jako wartość `data-val-rule` atrybutu. Istnieją także atrybuty formularza `data-val-ruleName-argumentName="argumentValue"` , które zawierają dodatkowe szczegóły dotyczące reguły, na przykład `data-val-maxlength-max="1024"` .

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Alternatywa pomocnika HTML dla pomocnika tagów wejściowych

`Html.TextBox`, `Html.TextBoxFor` `Html.Editor` i `Html.EditorFor` mają nakładające się funkcje pomocnika tagów wejściowych. Pomocnik tagu wejściowego automatycznie ustawi `type` atrybut `Html.TextBox` i `Html.TextBoxFor` nie będzie. `Html.Editor` i `Html.EditorFor` Obsługa kolekcji, złożonych obiektów i szablonów; pomocnik tagów wejściowych nie. Pomocnik tagu wejściowego `Html.EditorFor`  i  `Html.TextBoxFor` ma silnie wpisane (używa wyrażeń lambda); `Html.TextBox` i `Html.Editor` nie są (używają nazw wyrażeń).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()` i `@Html.EditorFor()` Użyj wpisu specjalnego `ViewDataDictionary` o nazwie `htmlAttributes` przy wykonywaniu ich domyślnych szablonów. To zachowanie jest opcjonalnie rozszerzane za pomocą `additionalViewData` parametrów. W kluczu "htmlAttributes" nie jest rozróżniana wielkość liter. Klucz "htmlAttributes" jest obsługiwany w podobny sposób, `htmlAttributes` jak obiekt przesłany do pomocników wejściowych, takich jak `@Html.TextBox()` .

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Nazwy wyrażeń

`asp-for`Wartość atrybutu jest `ModelExpression` i prawej strony wyrażenia lambda. W związku z tym, `asp-for="Property1"` zostanie `m => m.Property1` w wygenerowanym kodzie, dlatego nie musisz poprzedzać prefiksem `Model` . Można użyć \@ znaku "", aby rozpocząć wyrażenie śródwierszowe i przejść przed `m.` :

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

Generuje następujące elementy:

```html
<input type="text" id="joe" name="joe" value="Joe">
```

Właściwości kolekcji `asp-for="CollectionProperty[23].Member"` generują taką samą nazwę jak `asp-for="CollectionProperty[i].Member"` gdy `i` ma wartość `23` .

Gdy ASP.NET Core MVC oblicza wartość `ModelExpression` , sprawdza kilka źródeł, w tym `ModelState` . Weź pod uwagę `<input type="text" asp-for="@Name">` . Atrybut obliczeniowy `value` jest pierwszą wartością o wartości innej niż null z:

* `ModelState` wpis z kluczem "name".
* Wynik wyrażenia `Model.Name` .

### <a name="navigating-child-properties"></a>Nawigowanie po właściwościach podrzędnych

Możesz również przejść do właściwości podrzędnych przy użyciu ścieżki właściwości modelu widoku. Rozważmy bardziej złożoną klasę modelu, która zawiera `Address` Właściwość podrzędną.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

W widoku są powiązane `Address.AddressLine1` :

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

Następujący kod HTML jest generowany dla `Address.AddressLine1` :

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a>Nazwy wyrażeń i kolekcje

Przykładowy model zawierający tablicę `Colors` :

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

Metoda akcji:

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

Poniżej Razor pokazano, jak uzyskać dostęp do określonego `Color` elementu:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

Szablon *widoki/Shared/EditorTemplates/String. cshtml* :

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Przykład przy użyciu `List<T>` :

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

Poniżej Razor pokazano, jak wykonać iterację kolekcji:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

Szablon *widoki/Shared/EditorTemplates/ToDoItem. cshtml* :

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

`foreach` należy użyć, jeśli jest to możliwe, gdy wartość ma być używana w `asp-for` lub `Html.DisplayFor` równoważnym kontekście. Ogólnie rzecz biorąc, `for` jest lepiej niż (jeśli jest to możliwe `foreach` ), ponieważ nie musi alokować modułu wyliczającego, ale Ocena indeksatora w wyrażeniu LINQ może być kosztowna i powinna być zminimalizowana.

&nbsp;

>[!NOTE]
>Powyższy przykładowy kod z komentarzem pokazuje, jak zastąpić wyrażenie lambda `@` operatorem, aby uzyskać dostęp do każdego `ToDoItem` z nich na liście.

## <a name="the-textarea-tag-helper"></a>Pomocnik tagów TextArea

`Textarea Tag Helper`Pomocnik tagów jest podobny do pomocnika tagu wejściowego.

* Generuje `id` atrybuty i `name` i atrybuty walidacji danych z modelu dla [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) elementu.

* Zapewnia silne wpisywanie.

* Alternatywa dla pomocnika HTML: `Html.TextAreaFor`

Przykład:

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Następujący kod HTML jest generowany:

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a>Pomocnik tagów etykiety

* Generuje podpis etykiety i `for` atrybut w [\<label>](https://www.w3.org/wiki/HTML/Elements/label) elemencie dla nazwy wyrażenia

* Alternatywa dla pomocnika HTML: `Html.LabelFor` .

`Label Tag Helper`Zapewnia następujące korzyści w stosunku do czystego elementu etykiety HTML:

* Wartość etykiety opisowej jest automatycznie pobierana z `Display` atrybutu. Zamierzona nazwa wyświetlana może ulec zmianie z upływem czasu, a kombinacja `Display` pomocnika tagów atrybutów i etykiet będzie stosowała `Display` wszędzie tam, gdzie jest używana.

* Mniej znaczników w kodzie źródłowym

* Silne pisanie przy użyciu właściwości model.

Przykład:

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

Następujący kod HTML jest generowany dla `<label>` elementu:

```html
<label for="Email">Email Address</label>
```

Pomocnik tagu etykiety wygenerował `for` wartość atrybutu "e-mail", który jest identyfikatorem skojarzonym z `<input>` elementem. Pomocnicy tagów generują spójne `id` i `for` elementy, aby mogły być prawidłowo skojarzone. Podpis w tym przykładzie pochodzi z `Display` atrybutu. Jeśli model nie zawiera `Display` atrybutu, podpis będzie nazwą właściwości wyrażenia.

## <a name="the-validation-tag-helpers"></a>Pomocnicy tagów walidacji

Istnieją dwa pomocnicy tagów sprawdzania poprawności. `Validation Message Tag Helper`(Który wyświetla komunikat weryfikacyjny dla pojedynczej właściwości w modelu) i `Validation Summary Tag Helper` (która wyświetla podsumowanie błędów walidacji). `Input Tag Helper`Dodaje atrybuty walidacji po stronie klienta HTML5 do elementów wejściowych na podstawie atrybutów adnotacji danych klas modelu. Sprawdzanie poprawności jest również wykonywane na serwerze. Pomocnik tagów walidacji wyświetla te komunikaty o błędach w przypadku wystąpienia błędu walidacji.

### <a name="the-validation-message-tag-helper"></a>Pomocnik tagu komunikatu weryfikacji

* Dodaje atrybut [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` do elementu [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) , który dołącza komunikaty o błędach walidacji w polu wejściowym określonej właściwości modelu.   Gdy wystąpi błąd walidacji po stronie klienta, w elemencie [jQuery](https://jquery.com/) zostanie wyświetlony komunikat o błędzie `<span>` .

* Sprawdzanie poprawności odbywa się również na serwerze. Klienci mogą mieć wyłączone skrypty JavaScript, a niektóre sprawdzanie poprawności można wykonać tylko po stronie serwera.

* Alternatywa dla pomocnika HTML: `Html.ValidationMessageFor`

`Validation Message Tag Helper`Jest używany z `asp-validation-for` atrybutem elementu [zakresu](https://developer.mozilla.org/docs/Web/HTML/Element/span) html.

```cshtml
<span asp-validation-for="Email"></span>
```

Pomocnik tagu komunikatu weryfikacji wygeneruje następujący kod HTML:

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

Zwykle używasz `Validation Message Tag Helper` `Input` pomocnika tagu dla tej samej właściwości. Spowoduje to wyświetlenie wszystkich komunikatów o błędach weryfikacji obok danych wejściowych, które spowodowały błąd.

> [!NOTE]
> Musisz mieć widok z prawidłowymi odwołaniami do skryptów JavaScript i [jQuery](https://jquery.com/) na potrzeby weryfikacji po stronie klienta. Aby uzyskać więcej informacji, zobacz [Walidacja modelu](../models/validation.md) .

Gdy wystąpi błąd walidacji po stronie serwera (na przykład gdy istnieje niestandardowa Walidacja po stronie serwera lub weryfikacja po stronie klienta jest wyłączona), MVC umieszcza ten komunikat o błędzie jako treść `<span>` elementu.

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Pomocnik tagów podsumowania walidacji

* `<div>`Elementy docelowe z `asp-validation-summary` atrybutem

* Alternatywa dla pomocnika HTML: `@Html.ValidationSummary`

Służy `Validation Summary Tag Helper`  do wyświetlania podsumowania komunikatów weryfikacji. `asp-validation-summary`Wartość atrybutu może być dowolną z następujących:

|ASP-Walidacja — podsumowanie|Wyświetlane komunikaty weryfikacji|
|--- |--- |
|Podsumowania walidacji. All|Poziom właściwości i modelu|
|Podsumowania walidacji. ModelOnly|Model|
|Podsumowania walidacji. None|Brak|

### <a name="sample"></a>Przykład

W poniższym przykładzie model danych ma `DataAnnotation` atrybuty, które generują komunikaty o błędach walidacji w `<input>` elemencie.  Gdy wystąpi błąd walidacji, pomocnik tagów walidacji wyświetli komunikat o błędzie:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

Wygenerowany kod HTML (gdy model jest prawidłowy):

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a>Pomocnik wybierania tagu

* Generuje elementy [opcji](https://www.w3.org/wiki/HTML/Elements/option) [zaznaczania](https://www.w3.org/wiki/HTML/Elements/select) i skojarzone dla właściwości modelu.

* Ma alternatywę pomocnika HTML `Html.DropDownListFor` i `Html.ListBoxFor`

`Select Tag Helper` `asp-for` Określa nazwę właściwości modelu dla elementu [SELECT](https://www.w3.org/wiki/HTML/Elements/select) i `asp-items` określa elementy [opcji](https://www.w3.org/wiki/HTML/Elements/option) .  Na przykład:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Przykład:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

`Index`Metoda inicjuje `CountryViewModel` , ustawia wybrany kraj i przekazuje go do `Index` widoku.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

Metoda POST protokołu HTTP `Index` wyświetla zaznaczenie:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

`Index`Widok:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Który generuje następujący kod HTML (z wybranym "CA"):

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> Nie zalecamy używania `ViewBag` ani `ViewData` z pomocnikiem SELECT tag. Model widoku jest bardziej niezawodny przy udostępnianiu metadanych MVC i ogólnie mniej problematycznych.

`asp-for`Wartość atrybutu jest szczególnym przypadkiem i nie wymaga `Model` prefiksu, inne atrybuty pomocnika tagów to (na przykład `asp-items` ).

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Stałe powiązania

Jest to często wygodne do użycia `<select>` z `enum` właściwością i generują `SelectListItem` elementy z `enum` wartości.

Przykład:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

`GetEnumSelectList`Metoda generuje `SelectList` obiekt dla wyliczenia.

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Można oznaczyć listę modułów wyliczających `Display` atrybutem, aby uzyskać bogatszy interfejs użytkownika:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Następujący kod HTML jest generowany:

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a>Grupa opcji

Element HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) jest generowany, gdy model widoku zawiera jeden lub więcej `SelectListGroup` obiektów.

`CountryViewModelGroup`Grupuje `SelectListItem` elementy do grup "Ameryka Północna" i "Europa":

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Poniżej przedstawiono dwie grupy:

![przykład grupy opcji](working-with-forms/_static/grp.png)

Wygenerowany kod HTML:

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a>Wybór wielokrotny

Pomocnik Wybierz tag automatycznie generuje atrybut [wielokrotne = "Multiple"](https://w3c.github.io/html-reference/select.html)  , jeśli właściwość określona w `asp-for` atrybucie jest `IEnumerable` . Na przykład, uwzględniając następujący model:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

Z następującym widokiem:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Generuje następujący kod HTML:

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a>Brak zaznaczenia

Jeśli znajdziesz samodzielnie opcję "nie określono" na wielu stronach, możesz utworzyć szablon, aby wyeliminować powtarzanie kodu HTML:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

Szablon *widoki/Shared/EditorTemplates/CountryViewModel. cshtml* :

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

Dodawanie [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elementów HTML nie jest ograniczone do *żadnego przypadku zaznaczenia* . Na przykład następująca metoda widok i akcja spowoduje wygenerowanie kodu HTML podobnego do powyższego kodu:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

`<option>`Zostanie wybrany prawidłowy element (zawierający `selected="selected"` atrybut) w zależności od bieżącej `Country` wartości.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/views/tag-helpers/intro>
* [Element formularza HTML](https://www.w3.org/TR/html401/interact/forms.html)
* [Token weryfikacji żądania](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [IAttributeAdapter, interfejs](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [Fragmenty kodu dla tego dokumentu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
