---
title: Walidacja modelu w ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się więcej o walidacji modelu w ASP.NET Core MVC i Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: mvc/models/validation
ms.openlocfilehash: 0e3d4f4705dbfdae00943de2d85c603b6762a2f8
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205894"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a>Walidacja modelu w ASP.NET Core MVC i Razor Pages

::: moniker range=">= aspnetcore-3.0"

Autor [Kirka Larkin](https://github.com/serpent5)

W tym artykule wyjaśniono, jak sprawdzić poprawność danych wprowadzonych przez użytkownika w aplikacji ASP.NET Core MVC lub Razor Pages.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Stan modelu

Stan modelu reprezentuje błędy pochodzące z dwóch podsystemów: powiązanie modelu i walidacja modelu. Błędy, które pochodzą z [powiązania modelu](model-binding.md) , są zwykle Błędy konwersji danych. Na przykład znak "x" jest wprowadzany w polu liczby całkowitej. Walidacja modelu odbywa się po powiązaniu modelu i zgłasza błędy, gdy dane nie są zgodne z regułami biznesowymi. Na przykład wartość 0 jest wprowadzana w polu, które oczekuje klasyfikacji z przedziału od 1 do 5.

Powiązanie modelu i walidacja modelu są wykonywane przed wykonaniem akcji kontrolera lub metody obsługi Razor Pages. W przypadku aplikacji sieci Web jest odpowiedzialna za jej sprawdzenie `ModelState.IsValid` i reagowanie. Aplikacja internetowa zazwyczaj ponownie wyświetla stronę z komunikatem o błędzie:

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

Kontrolery interfejsu API sieci Web nie muszą `ModelState.IsValid` sprawdzać, `[ApiController]` czy mają atrybut. W takim przypadku automatyczna odpowiedź HTTP 400 zawierająca szczegóły błędu jest zwracana, gdy stan modelu jest nieprawidłowy. Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Uruchom ponownie weryfikację

Walidacja jest automatyczna, ale warto powtórzyć ją ręcznie. Na przykład można obliczyć wartość właściwości i chcieć ponownie uruchomić weryfikację po ustawieniu właściwości na wartość obliczaną. Aby ponownie uruchomić weryfikację, `TryValidateModel` Wywołaj metodę, jak pokazano poniżej:

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a>Atrybuty walidacji

Atrybuty walidacji umożliwiają określanie reguł walidacji dla właściwości modelu. W poniższym przykładzie z przykładowej aplikacji przedstawiono klasę modelu, która ma adnotację z atrybutami walidacji. Ten `[ClassicMovie]` atrybut jest niestandardowym atrybutem walidacji, a inne są wbudowane. Niepokazywany `[ClassicMovieWithClientValidator]`. `[ClassicMovieWithClientValidator]`pokazuje alternatywny sposób implementacji atrybutu niestandardowego.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a>Atrybuty wbudowane

Poniżej przedstawiono niektóre wbudowane atrybuty walidacji:

* `[CreditCard]`: Sprawdza, czy właściwość ma format karty kredytowej.
* `[Compare]`: Sprawdza, czy dwie właściwości w modelu pasują do siebie.
* `[EmailAddress]`: Sprawdza, czy właściwość ma format wiadomości e-mail.
* `[Phone]`: Sprawdza, czy właściwość ma format numeru telefonu.
* `[Range]`: Sprawdza, czy wartość właściwości znajduje się w określonym zakresie.
* `[RegularExpression]`: Sprawdza, czy wartość właściwości jest zgodna z określonym wyrażeniem regularnym.
* `[Required]`: Sprawdza, czy pole nie ma wartości null. Zobacz [ `[Required]` atrybut](#required-attribute) , aby uzyskać szczegółowe informacje o zachowaniu tego atrybutu.
* `[StringLength]`: Sprawdza, czy wartość właściwości String nie przekracza podanego limitu długości.
* `[Url]`: Sprawdza, czy właściwość ma format adresu URL.
* `[Remote]`: Sprawdza poprawność danych wejściowych na kliencie przez wywołanie metody akcji na serwerze. Zobacz [ `[Remote]` atrybut](#remote-attribute) , aby uzyskać szczegółowe informacje o zachowaniu tego atrybutu.

Pełną listę atrybutów sprawdzania poprawności można znaleźć w przestrzeni nazw [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .

### <a name="error-messages"></a>Komunikaty o błędach

Atrybuty walidacji pozwalają określić komunikat o błędzie, który ma być wyświetlany dla nieprawidłowych danych wejściowych. Przykład:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Wewnętrznie atrybuty są wywoływane `String.Format` przy użyciu symbolu zastępczego dla nazwy pola i czasami dodatkowych symboli zastępczych. Przykład:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

W przypadku zastosowania do `Name` właściwości komunikat o błędzie utworzony przez poprzedni kod powinien mieć wartość "nazwa musi zawierać się w przedziale od 6 do 8".

Aby dowiedzieć się, które parametry są `String.Format` przesyłane do komunikatu o błędzie określonego atrybutu, zobacz [kod źródłowy adnotacji](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)danych.

## <a name="required-attribute"></a>[Required] — atrybut

System sprawdzania poprawności w programie .NET Core 3,0 lub nowszy traktuje parametry niedopuszczające wartości null lub właściwości powiązane tak `[Required]` , jakby miały atrybut. [Typy wartości](/dotnet/csharp/language-reference/keywords/value-types) , takie `decimal` jak `int` i, nie dopuszczają wartości null. To zachowanie można wyłączyć przez skonfigurowanie <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> w programie `Startup.ConfigureServices`:

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a>[Wymagane] Walidacja na serwerze

Na serwerze, wymagana wartość jest uważana za brakującą, jeśli właściwość ma wartość null. Pole niedopuszczające wartości null jest zawsze prawidłowe i `[Required]` komunikat o błędzie nie jest wyświetlany.

Jednak powiązanie modelu dla właściwości niedopuszczających wartości null może zakończyć się niepowodzeniem, co spowoduje wystąpienie komunikatu `The value '' is invalid`o błędzie, takiego jak. Aby określić niestandardowy komunikat o błędzie dla weryfikacji po stronie serwera dla typów niedopuszczających wartości null, dostępne są następujące opcje:

* Wprowadź wartość pola Nullable (np. `decimal?` zamiast `decimal`). [Wartości\<null T>](/dotnet/csharp/programming-guide/nullable-types/) są traktowane jak standardowe typy dopuszczające wartości null.
* Określ domyślny komunikat o błędzie, który ma być używany przez powiązanie modelu, jak pokazano w następującym przykładzie:

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  Aby uzyskać więcej informacji o błędach powiązania modelu, dla których można ustawić domyślne komunikaty <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>dla programu, zobacz.

### <a name="required-validation-on-the-client"></a>[Wymagane] weryfikacja na kliencie

Typy niedopuszczające wartości null i ciągi są obsługiwane inaczej na kliencie w porównaniu z serwerem. Na kliencie:

* Wartość jest uważana za obecną tylko wtedy, gdy wprowadzono dla niej dane wejściowe. W związku z tym, walidacja po stronie klienta obsługuje niedopuszczające wartości null typy takie same jak typy dopuszczające wartości null.
* Biały znak w polu ciągu jest uznawany za prawidłowe dane wejściowe przez [wymaganą](https://jqueryvalidation.org/required-method/) metodę weryfikacji jQuery. Walidacja po stronie serwera traktuje wymagane pole ciągu nieprawidłowe, jeśli wprowadzono tylko odstępy.

Jak wspomniano wcześniej, typy niedopuszczające wartości null są traktowane tak, `[Required]` jakby miały atrybut. Oznacza to, że można uzyskać weryfikację po stronie klienta, nawet jeśli `[Required]` nie zastosowano atrybutu. Ale jeśli nie używasz tego atrybutu, zostanie wyświetlony domyślny komunikat o błędzie. Aby określić niestandardowy komunikat o błędzie, Użyj atrybutu.

## <a name="remote-attribute"></a>[Remote] — atrybut

Ten `[Remote]` atrybut implementuje walidację po stronie klienta, która wymaga wywołania metody na serwerze w celu określenia, czy dane wejściowe pola są prawidłowe. Na przykład aplikacja może wymagać sprawdzenia, czy nazwa użytkownika jest już używana.

Aby zaimplementować zdalne sprawdzanie poprawności:

1. Utwórz metodę akcji dla języka JavaScript do wywołania.  Metoda [zdalna](https://jqueryvalidation.org/remote-method/) walidacji jQuery oczekuje odpowiedzi JSON:

   * `true`oznacza, że dane wejściowe są prawidłowe.
   * `false`, `undefined`lub `null` oznacza, że dane wejściowe są nieprawidłowe. Wyświetl domyślny komunikat o błędzie.
   * Każdy inny ciąg oznacza, że dane wejściowe są nieprawidłowe. Wyświetl ciąg jako niestandardowy komunikat o błędzie.

   Oto przykład metody akcji, która zwraca niestandardowy komunikat o błędzie:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. W klasie modelu Dodaj adnotację do właściwości z `[Remote]` atrybutem wskazującym na metodę akcji walidacji, jak pokazano w następującym przykładzie:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   Ten `[Remote]` atrybut znajduje się w `Microsoft.AspNetCore.Mvc` przestrzeni nazw.
   
### <a name="additional-fields"></a>Dodatkowe pola

`AdditionalFields` Właściwość `[Remote]` atrybutu umożliwia Weryfikowanie kombinacji pól względem danych na serwerze. Na przykład jeśli `User` model ma `FirstName` i `LastName` właściwości, można sprawdzić, czy żaden istniejący użytkownik już ma tę parę nazw. Poniższy przykład przedstawia sposób użycia `AdditionalFields`:

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

`AdditionalFields`można jawnie ustawić dla ciągów "FirstName" i "LastName", ale użycie operatora [nameof](/dotnet/csharp/language-reference/keywords/nameof) upraszcza późniejsze refaktoryzacje. Metoda akcji dla tej walidacji musi akceptować `firstName` zarówno `lastName` argumenty, jak i:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Gdy użytkownik wprowadzi imię lub nazwisko, kod JavaScript wykonuje zdalne wywołanie, aby sprawdzić, czy ta para nazw została podjęta.

Aby sprawdzić poprawność dwóch lub więcej pól, podaj je jako listę rozdzielaną przecinkami. Na przykład aby dodać `MiddleName` właściwość do modelu, należy ustawić `[Remote]` atrybut, jak pokazano w następującym przykładzie:

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, podobnie jak wszystkie argumenty atrybutu, musi być wyrażeniem stałym. W związku z tym nie należy używać [interpolowanego ciągu](/dotnet/csharp/language-reference/keywords/interpolated-strings) ani <xref:System.String.Join*> wywołania `AdditionalFields`do inicjowania.

## <a name="alternatives-to-built-in-attributes"></a>Alternatywy dla wbudowanych atrybutów

Jeśli potrzebujesz weryfikacji niedostarczonej przez wbudowane atrybuty, możesz:

* [Tworzenie atrybutów niestandardowych](#custom-attributes).
* [Zaimplementuj IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Atrybuty niestandardowe

W przypadku scenariuszy, w których wbudowane atrybuty walidacji nie obsługują, można utworzyć niestandardowe atrybuty walidacji. Utwórz klasę, która dziedziczy z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, i Zastąp <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodę.

`IsValid` Metoda akceptuje obiekt o nazwie *Value*, czyli dane wejściowe do zweryfikowania. Przeciążenie akceptuje również `ValidationContext` obiekt, który zawiera dodatkowe informacje, takie jak wystąpienie modelu utworzone przez powiązanie modelu.

Poniższy przykład sprawdza, czy Data wydania filmu w *klasycznym* gatunku nie jest późniejsza niż określony rok. `[ClassicMovie]` Atrybut:

* Jest uruchamiany tylko na serwerze.
* W przypadku klasycznych filmów program sprawdza poprawność daty wydania:

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

`movie` Zmienna w poprzednim przykładzie reprezentuje `Movie` obiekt, który zawiera dane z przesłania formularza. Gdy Walidacja nie powiedzie się, `ValidationResult` zostanie zwrócony komunikat o błędzie.

## <a name="ivalidatableobject"></a>IValidatableObject

Poprzedni przykład działa tylko z `Movie` typami. Kolejną opcją weryfikacji na poziomie klasy jest implementacja `IValidatableObject` w klasie modelu, jak pokazano w następującym przykładzie:

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Sprawdzanie poprawności węzła najwyższego poziomu

Węzły najwyższego poziomu obejmują:

* Parametry akcji
* Właściwości kontrolera
* Parametry procedury obsługi stron
* Właściwości modelu strony

Węzły najwyższego poziomu powiązane z modelem są weryfikowane jako uzupełnienie właściwości modelu. W poniższym przykładzie z przykładowej aplikacji `VerifyPhone` Metoda używa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> do walidacji parametru `phone` Action:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Węzły najwyższego poziomu mogą <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> korzystać z atrybutów walidacji. W poniższym przykładzie z przykładowej aplikacji `CheckAge` Metoda określa, że `age` parametr musi być powiązany z ciągu zapytania, gdy formularz zostanie przesłany:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

Na stronie sprawdzanie wieku (*Sprawdź. cshtml*) Istnieją dwa formy. Pierwszy formularz przesyła `Age` wartość `99` jako parametr ciągu zapytania:. `https://localhost:5001/Users/CheckAge?Age=99`

Po przesłaniu poprawnie `age` sformatowanego parametru z ciągu zapytania, formularz sprawdza poprawność.

Drugi formularz na stronie sprawdzanie wieku przesyła `Age` wartość w treści żądania, a Walidacja nie powiedzie się. Powiązanie nie powiodło `age` się, ponieważ parametr musi pochodzić z ciągu zapytania.

## <a name="maximum-errors"></a>Maksymalna liczba błędów

Walidacja jest zatrzymywana, gdy zostanie osiągnięta maksymalna liczba błędów (domyślnie 200). Tę liczbę można skonfigurować przy użyciu następującego kodu w programie `Startup.ConfigureServices`:

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a>Maksymalna rekursja

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>przechodzi do grafu obiektów sprawdzanego przez model. W przypadku modeli, które są głębokie lub nieskończonie cykliczne, walidacja może spowodować przepełnienie stosu. [MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) umożliwia szybkie zakończenie sprawdzania poprawności, Jeśli rekursja odwiedzających przekracza skonfigurowaną głębokość. Wartość domyślna `MvcOptions.MaxValidationDepth` to 32.

## <a name="automatic-short-circuit"></a>Automatyczny krótki obwód

Sprawdzanie poprawności jest automatycznie skracane (pomijane), jeśli wykres modelu nie wymaga weryfikacji. Obiekty, dla których środowisko uruchomieniowe pomija sprawdzanie poprawności dla dołączania kolekcji elementów `byte[]`podstawowych `string[]`( `Dictionary<string, string>`takich jak,,) i złożonych wykresów obiektów, które nie mają żadnych modułów sprawdzania poprawności.

## <a name="disable-validation"></a>Wyłącz weryfikację

Aby wyłączyć weryfikację:

1. Utwórz implementację `IObjectModelValidator` , która nie oznacza żadnych pól jako nieprawidłowych.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. Dodaj następujący kod, aby `Startup.ConfigureServices` zastąpić domyślną `IObjectModelValidator` implementację w kontenerze iniekcji zależności.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Nadal mogą pojawić się błędy stanu modelu pochodzące z powiązania modelu.

## <a name="client-side-validation"></a>Weryfikacja po stronie klienta

Weryfikacja po stronie klienta uniemożliwia przesyłanie, dopóki formularz nie będzie prawidłowy. Przycisk Prześlij uruchamia kod JavaScript, który przesyła formularz lub wyświetla komunikaty o błędach.

Weryfikacja po stronie klienta umożliwia uniknięcie niepotrzebnej komunikacji z serwerem w przypadku błędów danych wejściowych w formularzu. Następujący skrypt odwołuje się do *_Layout. cshtml* i *_ValidationScriptsPartial. cshtml* obsługuje walidację po stronie klienta:

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

Skrypt [niezauważalnego sprawdzania poprawności jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) jest niestandardową biblioteką frontonu firmy Microsoft, która kompiluje się w popularnej wersji interfejsu [jQuery](https://jqueryvalidation.org/) . Bez dyskretnej weryfikacji jQuery należy wykonać kod tej samej logiki walidacji w dwóch miejscach: raz w atrybuty walidacji po stronie serwera we właściwościach modelu, a następnie ponownie w skryptach po stronie klienta. Zamiast tego, [pomocników tagów](xref:mvc/views/tag-helpers/intro) i [pomocników HTML](xref:mvc/views/overview) używają atrybutów walidacji i metadanych typu z właściwości modelu do renderowania atrybutów `data-` HTML 5 dla elementów formularza, które wymagają walidacji. niezauważalne sprawdzenie poprawności przez `data-` program jQuery umożliwia przeanalizowanie atrybutów i przekazanie logiki do programu jQuery Validate, efektywne "Kopiowanie" logiki walidacji po stronie serwera do klienta. Błędy sprawdzania poprawności można wyświetlić na kliencie przy użyciu pomocników tagów, jak pokazano poniżej:

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

Poprzednie pomocnicy tagów renderują następujący kod HTML:

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

Zauważ, że `data-` atrybuty w danych wyjściowych HTML odpowiadają atrybutom walidacji `Movie.ReleaseDate` właściwości. Ten `data-val-required` atrybut zawiera komunikat o błędzie, który zostanie wyświetlony, jeśli użytkownik nie wypełni pola Data wydania. niezauważalne sprawdzenie poprawności przez funkcję jQuery powoduje, że ta wartość jest przekazywana do walidacji [wymaganej metody ()](https://jqueryvalidation.org/required-method/) , która następnie wyświetla ten komunikat w towarzyszącym ** \<zakresie>** elementu.

Walidacja typu danych jest oparta na typie .NET właściwości, chyba że zostanie zastąpiona przez `[DataType]` atrybut. Przeglądarki mają własne domyślne komunikaty o błędach, ale pakietem weryfikacji jQuery nie dyskretnego sprawdzania poprawności może przesłonić te komunikaty. `[DataType]`atrybuty i podklasy, takie jak `[EmailAddress]` pozwalają określić komunikat o błędzie.

## <a name="unobtrusive-validation"></a>Niezauważalna weryfikacja

Aby uzyskać informacje o niezauważalnej weryfikacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/1111)w usłudze GitHub.

### <a name="add-validation-to-dynamic-forms"></a>Dodawanie walidacji do formularzy dynamicznych

w przypadku niedyskretnego sprawdzania poprawności jest sprawdzana logika walidacji i parametry do jQuery podczas pierwszego ładowania strony. W związku z tym sprawdzanie poprawności nie działa automatycznie na formularzach generowanych dynamicznie. Aby włączyć weryfikację, poinformuj jQuery o niezauważalnej weryfikacji, aby przeanalizować formularz dynamiczny bezpośrednio po jego utworzeniu. Na przykład poniższy kod konfiguruje walidację po stronie klienta w formularzu dodanym przez AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

`$.validator.unobtrusive.parse()` Metoda akceptuje selektor jQuery dla jednego argumentu. Ta metoda informuje niedyskretną weryfikację jQuery do `data-` analizy atrybutów formularzy w ramach tego selektora. Wartości tych atrybutów są następnie przesyłane do wtyczki do walidacji jQuery.

### <a name="add-validation-to-dynamic-controls"></a>Dodawanie walidacji do formantów dynamicznych

`$.validator.unobtrusive.parse()` Metoda działa na całym formularzu, a nie na poszczególnych dynamicznie generowanych kontrolkach, takich `<input>` jak `<select/>`i. Aby przeanalizować formularz, Usuń dane sprawdzania poprawności, które zostały dodane, gdy formularz został wcześniej przeanalizowany, jak pokazano w następującym przykładzie:

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Niestandardowe sprawdzanie poprawności po stronie klienta

Niestandardowe sprawdzanie poprawności po stronie klienta jest wykonywane przez `data-` generowanie atrybutów HTML, które działają z niestandardowym identyfikatorem platformy jQuery. Następujący przykładowy kod karty został zapisany dla atrybutów `[ClassicMovie]` i `[ClassicMovieWithClientValidator]` , które zostały wprowadzone wcześniej w tym artykule:

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

Aby uzyskać informacje o sposobach pisania kart sieciowych, zobacz [dokumentację dotyczącą platformy jQuery Validate](https://jqueryvalidation.org/documentation/).

Użycie karty dla danego pola jest wyzwalane przez `data-` atrybuty, które:

* Oznacz pole jako podlegające walidacji (`data-val="true"`).
* Zidentyfikuj nazwę reguły walidacji i tekst komunikatu o błędzie (na przykład `data-val-rulename="Error message."`).
* Podaj wszelkie dodatkowe parametry wymagane przez moduł sprawdzania poprawności (na `data-val-rulename-param1="value"`przykład).

W poniższym przykładzie przedstawiono `data-` atrybuty dla `ClassicMovie` atrybutu przykładowej aplikacji:

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

Jak wspomniano wcześniej, [pomocników tagów](xref:mvc/views/tag-helpers/intro) i [pomocników HTML](xref:mvc/views/overview) wykorzystują informacje z atrybutów walidacji do renderowania `data-` atrybutów. Istnieją dwie opcje pisania kodu, które powoduje tworzenie niestandardowych `data-` atrybutów HTML:

* Utwórz klasę, która pochodzi od `AttributeAdapterBase<TAttribute>` i klasy implementującej `IValidationAttributeAdapterProvider`, i Zarejestruj swój atrybut oraz jego kartę w programie di. Ta metoda jest zgodna z [pojedynczym podmiotem odpowiedzialnym](https://wikipedia.org/wiki/Single_responsibility_principle) w odniesieniu do kodu weryfikacyjnego związanego z serwerem i klienta jest w osobnych klasach. Adapter ma również zalety, że ponieważ jest on zarejestrowany w programie DI, w razie potrzeby są dostępne inne usługi w programie DI.
* Zaimplementuj `IClientModelValidator` w `ValidationAttribute` klasie. Ta metoda może być odpowiednia, jeśli atrybut nie wykonuje walidacji po stronie serwera i nie wymaga żadnych usług z programu DI.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter dla weryfikacji po stronie klienta

Ta metoda renderowania `data-` atrybutów w kodzie HTML jest używana przez `ClassicMovie` atrybut w przykładowej aplikacji. Aby dodać weryfikację klienta przy użyciu tej metody:

1. Utwórz klasę adaptera atrybutów dla niestandardowego atrybutu walidacji. Utwórz klasę z [\<AttributeAdapterBase T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2). Utwórz `AddValidation` metodę, która dodaje `data-` atrybuty do renderowanych danych wyjściowych, jak pokazano w tym przykładzie:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. Utwórz klasę dostawcy kart implementującą <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. W `GetAttributeAdapter` metodzie Przekaż atrybut niestandardowy do konstruktora adaptera, jak pokazano w tym przykładzie:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. Zarejestruj dostawcę karty dla DI in `Startup.ConfigureServices`:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator dla weryfikacji po stronie klienta

Ta metoda renderowania `data-` atrybutów w kodzie HTML jest używana przez `ClassicMovieWithClientValidator` atrybut w przykładowej aplikacji. Aby dodać weryfikację klienta przy użyciu tej metody:

* W atrybucie niestandardowego sprawdzania poprawności Zaimplementuj `IClientModelValidator` interfejs i Utwórz `AddValidation` metodę. W `AddValidation` metodzie Dodaj `data-` atrybuty do walidacji, jak pokazano w następującym przykładzie:

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a>Wyłącz weryfikację po stronie klienta

Poniższy kod wyłącza weryfikację klienta w Razor Pages:

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

Inne opcje wyłączenia weryfikacji po stronie klienta:

* Dodaj komentarz do odwołania do `_ValidationScriptsPartial` wszystkich plików *. cshtml* .
* Usuń zawartość pliku *Pages\Shared\_ValidationScriptsPartial. cshtml* .

Poprzednie podejście nie zapobiega weryfikacji po stronie klienta w bibliotece klas Razor ASP.NET Core Identity. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przestrzeń nazw System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Powiązanie modelu](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule wyjaśniono, jak sprawdzić poprawność danych wprowadzonych przez użytkownika w aplikacji ASP.NET Core MVC lub Razor Pages.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Stan modelu

Stan modelu reprezentuje błędy pochodzące z dwóch podsystemów: powiązanie modelu i walidacja modelu. Błędy, które pochodzą z [powiązania modelu](model-binding.md) , to zwykle Błędy konwersji danych (na przykład "x" jest wprowadzany w polu, w którym jest oczekiwana liczba całkowita). Walidacja modelu odbywa się po powiązaniu modelu i zgłasza błędy, gdy dane nie są zgodne z regułami biznesowymi (na przykład wartość 0 jest wprowadzana w polu, w którym oczekiwana jest Ocena z zakresu od 1 do 5).

Zarówno powiązanie modelu, jak i walidacja są wykonywane przed wykonaniem akcji kontrolera lub metody obsługi Razor Pages. W przypadku aplikacji sieci Web jest odpowiedzialna za jej sprawdzenie `ModelState.IsValid` i reagowanie. Aplikacja internetowa zazwyczaj ponownie wyświetla stronę z komunikatem o błędzie:

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

Kontrolery interfejsu API sieci Web nie muszą `ModelState.IsValid` sprawdzać, `[ApiController]` czy mają atrybut. W takim przypadku automatyczna odpowiedź HTTP 400 zawierająca szczegóły błędu jest zwracana, gdy stan modelu jest nieprawidłowy. Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Uruchom ponownie weryfikację

Walidacja jest automatyczna, ale warto powtórzyć ją ręcznie. Na przykład można obliczyć wartość właściwości i chcieć ponownie uruchomić weryfikację po ustawieniu właściwości na wartość obliczaną. Aby ponownie uruchomić weryfikację, `TryValidateModel` Wywołaj metodę, jak pokazano poniżej:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Atrybuty walidacji

Atrybuty walidacji umożliwiają określanie reguł walidacji dla właściwości modelu. W poniższym przykładzie z [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) przedstawiono klasę modelu, która ma adnotację z atrybutami walidacji. Ten `[ClassicMovie]` atrybut jest niestandardowym atrybutem walidacji, a inne są wbudowane. Niepokazywany `[ClassicMovie2]`jest, który pokazuje alternatywny sposób implementacji atrybutu niestandardowego.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Atrybuty wbudowane

Wbudowane atrybuty walidacji obejmują:

* `[CreditCard]`: Sprawdza, czy właściwość ma format karty kredytowej.
* `[Compare]`: Sprawdza, czy dwie właściwości w modelu pasują do siebie. Na przykład plik *register.cshtml.cs* używa `[Compare]` do sprawdzania poprawności dwóch wprowadzonych haseł. [Tożsamość szkieletu](xref:security/authentication/scaffold-identity) do wyświetlania kodu rejestru.
* `[EmailAddress]`: Sprawdza, czy właściwość ma format wiadomości e-mail.
* `[Phone]`: Sprawdza, czy właściwość ma format numeru telefonu.
* `[Range]`: Sprawdza, czy wartość właściwości znajduje się w określonym zakresie.
* `[RegularExpression]`: Sprawdza, czy wartość właściwości jest zgodna z określonym wyrażeniem regularnym.
* `[Required]`: Sprawdza, czy pole nie ma wartości null. Zobacz [ `[Required]` atrybut](#required-attribute) , aby uzyskać szczegółowe informacje o zachowaniu tego atrybutu.
* `[StringLength]`: Sprawdza, czy wartość właściwości String nie przekracza podanego limitu długości.
* `[Url]`: Sprawdza, czy właściwość ma format adresu URL.
* `[Remote]`: Sprawdza poprawność danych wejściowych na kliencie przez wywołanie metody akcji na serwerze. Zobacz [ `[Remote]` atrybut](#remote-attribute) , aby uzyskać szczegółowe informacje o zachowaniu tego atrybutu.

W przypadku używania `[RegularExpression]` atrybutu z walidacją po stronie klienta wyrażenie regularne jest wykonywane w języku JavaScript na kliencie. Oznacza to, że będzie używane zachowanie zgodne ze standardem [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) . Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/corefx/issues/42487)w serwisie GitHub.

Pełną listę atrybutów sprawdzania poprawności można znaleźć w przestrzeni nazw [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .

### <a name="error-messages"></a>Komunikaty o błędach

Atrybuty walidacji pozwalają określić komunikat o błędzie, który ma być wyświetlany dla nieprawidłowych danych wejściowych. Przykład:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Wewnętrznie atrybuty są wywoływane `String.Format` przy użyciu symbolu zastępczego dla nazwy pola i czasami dodatkowych symboli zastępczych. Przykład:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

W przypadku zastosowania do `Name` właściwości komunikat o błędzie utworzony przez poprzedni kod powinien mieć wartość "nazwa musi zawierać się w przedziale od 6 do 8".

Aby dowiedzieć się, które parametry są `String.Format` przesyłane do komunikatu o błędzie określonego atrybutu, zobacz [kod źródłowy adnotacji](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)danych.

## <a name="required-attribute"></a>[Required] — atrybut

Domyślnie system walidacji traktuje niedopuszczające wartości null parametry lub właściwości tak, jakby miały `[Required]` atrybut. [Typy wartości](/dotnet/csharp/language-reference/keywords/value-types) , takie `decimal` jak `int` i, nie dopuszczają wartości null.

### <a name="required-validation-on-the-server"></a>[Wymagane] Walidacja na serwerze

Na serwerze, wymagana wartość jest uważana za brakującą, jeśli właściwość ma wartość null. Pole niedopuszczające wartości null jest zawsze prawidłowe, a komunikat o błędzie [Required] nie jest nigdy wyświetlany.

Jednak powiązanie modelu dla właściwości niedopuszczających wartości null może zakończyć się niepowodzeniem, co spowoduje wystąpienie komunikatu `The value '' is invalid`o błędzie, takiego jak. Aby określić niestandardowy komunikat o błędzie dla weryfikacji po stronie serwera dla typów niedopuszczających wartości null, dostępne są następujące opcje:

* Wprowadź wartość pola Nullable (np. `decimal?` zamiast `decimal`). [Wartości\<null T>](/dotnet/csharp/programming-guide/nullable-types/) są traktowane jak standardowe typy dopuszczające wartości null.
* Określ domyślny komunikat o błędzie, który ma być używany przez powiązanie modelu, jak pokazano w następującym przykładzie:

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  Aby uzyskać więcej informacji o błędach powiązania modelu, dla których można ustawić domyślne komunikaty <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>dla programu, zobacz.

### <a name="required-validation-on-the-client"></a>[Wymagane] weryfikacja na kliencie

Typy niedopuszczające wartości null i ciągi są obsługiwane inaczej na kliencie w porównaniu z serwerem. Na kliencie:

* Wartość jest uważana za obecną tylko wtedy, gdy wprowadzono dla niej dane wejściowe. W związku z tym, walidacja po stronie klienta obsługuje niedopuszczające wartości null typy takie same jak typy dopuszczające wartości null.
* Biały znak w polu ciągu jest uznawany za prawidłowe dane wejściowe przez [wymaganą](https://jqueryvalidation.org/required-method/) metodę weryfikacji jQuery. Walidacja po stronie serwera traktuje wymagane pole ciągu nieprawidłowe, jeśli wprowadzono tylko odstępy.

Jak wspomniano wcześniej, typy niedopuszczające wartości null są traktowane tak, `[Required]` jakby miały atrybut. Oznacza to, że można uzyskać weryfikację po stronie klienta, nawet jeśli `[Required]` nie zastosowano atrybutu. Ale jeśli nie używasz tego atrybutu, zostanie wyświetlony domyślny komunikat o błędzie. Aby określić niestandardowy komunikat o błędzie, Użyj atrybutu.

## <a name="remote-attribute"></a>[Remote] — atrybut

Ten `[Remote]` atrybut implementuje walidację po stronie klienta, która wymaga wywołania metody na serwerze w celu określenia, czy dane wejściowe pola są prawidłowe. Na przykład aplikacja może wymagać sprawdzenia, czy nazwa użytkownika jest już używana.

Aby zaimplementować zdalne sprawdzanie poprawności:

1. Utwórz metodę akcji dla języka JavaScript do wywołania.  Metoda [zdalna](https://jqueryvalidation.org/remote-method/) walidacji jQuery oczekuje odpowiedzi JSON:

   * `"true"`oznacza, że dane wejściowe są prawidłowe.
   * `"false"`, `undefined`lub `null` oznacza, że dane wejściowe są nieprawidłowe.  Wyświetl domyślny komunikat o błędzie.
   * Każdy inny ciąg oznacza, że dane wejściowe są nieprawidłowe. Wyświetl ciąg jako niestandardowy komunikat o błędzie.

   Oto przykład metody akcji, która zwraca niestandardowy komunikat o błędzie:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. W klasie modelu Dodaj adnotację do właściwości z `[Remote]` atrybutem wskazującym na metodę akcji walidacji, jak pokazano w następującym przykładzie:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   Ten `[Remote]` atrybut znajduje się w `Microsoft.AspNetCore.Mvc` przestrzeni nazw. Zainstaluj pakiet NuGet [Microsoft. AspNetCore. MVC. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) , jeśli nie używasz programu `Microsoft.AspNetCore.App` lub `Microsoft.AspNetCore.All` pakietu.
   
### <a name="additional-fields"></a>Dodatkowe pola

`AdditionalFields` Właściwość `[Remote]` atrybutu umożliwia Weryfikowanie kombinacji pól względem danych na serwerze. Na przykład jeśli `User` model ma `FirstName` i `LastName` właściwości, można sprawdzić, czy żaden istniejący użytkownik już ma tę parę nazw. Poniższy przykład przedstawia sposób użycia `AdditionalFields`:

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields`może być jawnie ustawiona dla ciągów `"FirstName"` i `"LastName"`, ale użycie operatora [nameof](/dotnet/csharp/language-reference/keywords/nameof) upraszcza późniejsze refaktoryzacje. Metoda akcji dla tej weryfikacji musi akceptować zarówno imiona, jak i nazwiska:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Gdy użytkownik wprowadzi imię lub nazwisko, kod JavaScript wykonuje zdalne wywołanie, aby sprawdzić, czy ta para nazw została podjęta.

Aby sprawdzić poprawność dwóch lub więcej pól, podaj je jako listę rozdzielaną przecinkami. Na przykład aby dodać `MiddleName` właściwość do modelu, należy ustawić `[Remote]` atrybut, jak pokazano w następującym przykładzie:

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, podobnie jak wszystkie argumenty atrybutu, musi być wyrażeniem stałym. W związku z tym nie należy używać [interpolowanego ciągu](/dotnet/csharp/language-reference/keywords/interpolated-strings) ani <xref:System.String.Join*> wywołania `AdditionalFields`do inicjowania.

## <a name="alternatives-to-built-in-attributes"></a>Alternatywy dla wbudowanych atrybutów

Jeśli potrzebujesz weryfikacji niedostarczonej przez wbudowane atrybuty, możesz:

* [Tworzenie atrybutów niestandardowych](#custom-attributes).
* [Zaimplementuj IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Atrybuty niestandardowe

W przypadku scenariuszy, w których wbudowane atrybuty walidacji nie obsługują, można utworzyć niestandardowe atrybuty walidacji. Utwórz klasę, która dziedziczy z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, i Zastąp <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodę.

`IsValid` Metoda akceptuje obiekt o nazwie *Value*, czyli dane wejściowe do zweryfikowania. Przeciążenie akceptuje również `ValidationContext` obiekt, który zawiera dodatkowe informacje, takie jak wystąpienie modelu utworzone przez powiązanie modelu.

Poniższy przykład sprawdza, czy Data wydania filmu w *klasycznym* gatunku nie jest późniejsza niż określony rok. Ten `[ClassicMovie2]` atrybut najpierw sprawdza gatunek i kontynuuje działanie tylko wtedy, gdy jest on *klasyczny*. W przypadku filmów identyfikowanych jako Classics sprawdza datę wydania, aby upewnić się, że nie jest ona późniejsza niż limit przesłany do konstruktora atrybutu.

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

`movie` Zmienna w poprzednim przykładzie reprezentuje `Movie` obiekt, który zawiera dane z przesłania formularza. `IsValid` Metoda sprawdza datę i gatunek. Po pomyślnej `IsValid` weryfikacji program `ValidationResult.Success` zwraca kod. Gdy Walidacja nie powiedzie się, `ValidationResult` zostanie zwrócony komunikat o błędzie.

## <a name="ivalidatableobject"></a>IValidatableObject

Poprzedni przykład działa tylko z `Movie` typami. Kolejną opcją weryfikacji na poziomie klasy jest implementacja `IValidatableObject` w klasie modelu, jak pokazano w następującym przykładzie:

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Sprawdzanie poprawności węzła najwyższego poziomu

Węzły najwyższego poziomu obejmują:

* Parametry akcji
* Właściwości kontrolera
* Parametry procedury obsługi stron
* Właściwości modelu strony

Węzły najwyższego poziomu powiązane z modelem są weryfikowane jako uzupełnienie właściwości modelu. W poniższym przykładzie z przykładowej aplikacji `VerifyPhone` Metoda używa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> do walidacji parametru `phone` Action:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Węzły najwyższego poziomu mogą <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> korzystać z atrybutów walidacji. W poniższym przykładzie z przykładowej aplikacji `CheckAge` Metoda określa, że `age` parametr musi być powiązany z ciągu zapytania, gdy formularz zostanie przesłany:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

Na stronie sprawdzanie wieku (*Sprawdź. cshtml*) Istnieją dwa formy. Pierwszy formularz przesyła `Age` wartość `99` jako ciąg zapytania:. `https://localhost:5001/Users/CheckAge?Age=99`

Po przesłaniu poprawnie `age` sformatowanego parametru z ciągu zapytania, formularz sprawdza poprawność.

Drugi formularz na stronie sprawdzanie wieku przesyła `Age` wartość w treści żądania, a Walidacja nie powiedzie się. Powiązanie nie powiodło `age` się, ponieważ parametr musi pochodzić z ciągu zapytania.

W przypadku korzystania `CompatibilityVersion.Version_2_1` z programu lub nowszego Walidacja węzła najwyższego poziomu jest domyślnie włączona. W przeciwnym razie Walidacja węzła najwyższego poziomu jest wyłączona. Opcję domyślną można przesłonić, ustawiając <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> właściwość w (`Startup.ConfigureServices`), jak pokazano poniżej:

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>Maksymalna liczba błędów

Walidacja jest zatrzymywana, gdy zostanie osiągnięta maksymalna liczba błędów (domyślnie 200). Tę liczbę można skonfigurować przy użyciu następującego kodu w programie `Startup.ConfigureServices`:

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>Maksymalna rekursja

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>przechodzi do grafu obiektów sprawdzanego przez model. W przypadku modeli, które są bardzo głębokie lub nieskończonie cykliczne, walidacja może spowodować przepełnienie stosu. [MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) umożliwia szybkie zakończenie sprawdzania poprawności, Jeśli rekursja odwiedzających przekracza skonfigurowaną głębokość. Wartość domyślna `MvcOptions.MaxValidationDepth` to 32 w przypadku uruchamiania z `CompatibilityVersion.Version_2_2` systemem lub nowszym. W przypadku wcześniejszych wersji wartość jest równa null, co oznacza brak ograniczenia głębokości.

## <a name="automatic-short-circuit"></a>Automatyczny krótki obwód

Sprawdzanie poprawności jest automatycznie skracane (pomijane), jeśli wykres modelu nie wymaga weryfikacji. Obiekty, dla których środowisko uruchomieniowe pomija sprawdzanie poprawności dla dołączania kolekcji elementów `byte[]`podstawowych `string[]`( `Dictionary<string, string>`takich jak,,) i złożonych wykresów obiektów, które nie mają żadnych modułów sprawdzania poprawności.

## <a name="disable-validation"></a>Wyłącz weryfikację

Aby wyłączyć weryfikację:

1. Utwórz implementację `IObjectModelValidator` , która nie oznacza żadnych pól jako nieprawidłowych.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. Dodaj następujący kod, aby `Startup.ConfigureServices` zastąpić domyślną `IObjectModelValidator` implementację w kontenerze iniekcji zależności.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Nadal mogą pojawić się błędy stanu modelu pochodzące z powiązania modelu.

## <a name="client-side-validation"></a>Weryfikacja po stronie klienta

Weryfikacja po stronie klienta uniemożliwia przesyłanie, dopóki formularz nie będzie prawidłowy. Przycisk Prześlij uruchamia kod JavaScript, który przesyła formularz lub wyświetla komunikaty o błędach.

Weryfikacja po stronie klienta umożliwia uniknięcie niepotrzebnej komunikacji z serwerem w przypadku błędów danych wejściowych w formularzu. Następujący skrypt odwołuje się do *_Layout. cshtml* i *_ValidationScriptsPartial. cshtml* obsługuje walidację po stronie klienta:

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

Skrypt [niezauważalnego sprawdzania poprawności jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) jest niestandardową biblioteką frontonu firmy Microsoft, która kompiluje się w popularnej wersji interfejsu [jQuery](https://jqueryvalidation.org/) . Bez dyskretnej weryfikacji jQuery należy wykonać kod tej samej logiki walidacji w dwóch miejscach: raz w atrybuty walidacji po stronie serwera we właściwościach modelu, a następnie ponownie w skryptach po stronie klienta. Zamiast tego, [pomocników tagów](xref:mvc/views/tag-helpers/intro) i [pomocników HTML](xref:mvc/views/overview) używają atrybutów walidacji i metadanych typu z właściwości modelu do renderowania atrybutów `data-` HTML 5 dla elementów formularza, które wymagają walidacji. niezauważalne sprawdzenie poprawności przez `data-` program jQuery umożliwia przeanalizowanie atrybutów i przekazanie logiki do programu jQuery Validate, efektywne "Kopiowanie" logiki walidacji po stronie serwera do klienta. Błędy sprawdzania poprawności można wyświetlić na kliencie przy użyciu pomocników tagów, jak pokazano poniżej:

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Poprzednie pomocnicy tagów renderują następujący kod HTML.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Zauważ, że `data-` atrybuty w danych wyjściowych HTML odpowiadają atrybutom walidacji `ReleaseDate` właściwości. Ten `data-val-required` atrybut zawiera komunikat o błędzie, który zostanie wyświetlony, jeśli użytkownik nie wypełni pola Data wydania. niezauważalne sprawdzenie poprawności przez funkcję jQuery powoduje, że ta wartość jest przekazywana do walidacji [wymaganej metody ()](https://jqueryvalidation.org/required-method/) , która następnie wyświetla ten komunikat w towarzyszącym ** \<zakresie>** elementu.

Walidacja typu danych jest oparta na typie .NET właściwości, chyba że zostanie zastąpiona przez `[DataType]` atrybut. Przeglądarki mają własne domyślne komunikaty o błędach, ale pakietem weryfikacji jQuery nie dyskretnego sprawdzania poprawności może przesłonić te komunikaty. `[DataType]`atrybuty i podklasy, takie jak `[EmailAddress]` pozwalają określić komunikat o błędzie.

### <a name="add-validation-to-dynamic-forms"></a>Dodawanie walidacji do formularzy dynamicznych

w przypadku niedyskretnego sprawdzania poprawności jest sprawdzana logika walidacji i parametry do jQuery podczas pierwszego ładowania strony. W związku z tym sprawdzanie poprawności nie działa automatycznie na formularzach generowanych dynamicznie. Aby włączyć weryfikację, poinformuj jQuery o niezauważalnej weryfikacji, aby przeanalizować formularz dynamiczny bezpośrednio po jego utworzeniu. Na przykład poniższy kod konfiguruje walidację po stronie klienta w formularzu dodanym przez AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

`$.validator.unobtrusive.parse()` Metoda akceptuje selektor jQuery dla jednego argumentu. Ta metoda informuje niedyskretną weryfikację jQuery do `data-` analizy atrybutów formularzy w ramach tego selektora. Wartości tych atrybutów są następnie przesyłane do wtyczki do walidacji jQuery.

### <a name="add-validation-to-dynamic-controls"></a>Dodawanie walidacji do formantów dynamicznych

`$.validator.unobtrusive.parse()` Metoda działa na całym formularzu, a nie na poszczególnych dynamicznie generowanych kontrolkach, takich `<input>` jak `<select/>`i. Aby przeanalizować formularz, Usuń dane sprawdzania poprawności, które zostały dodane, gdy formularz został wcześniej przeanalizowany, jak pokazano w następującym przykładzie:

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Niestandardowe sprawdzanie poprawności po stronie klienta

Niestandardowe sprawdzanie poprawności po stronie klienta jest wykonywane przez `data-` generowanie atrybutów HTML, które działają z niestandardowym identyfikatorem platformy jQuery. Następujący przykładowy kod karty został zapisany dla atrybutów `ClassicMovie` i `ClassicMovie2` , które zostały wprowadzone wcześniej w tym artykule:

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

Aby uzyskać informacje o sposobach pisania kart sieciowych, zobacz [dokumentację dotyczącą platformy jQuery Validate](https://jqueryvalidation.org/documentation/).

Użycie karty dla danego pola jest wyzwalane przez `data-` atrybuty, które:

* Oznacz pole jako podlegające walidacji (`data-val="true"`).
* Zidentyfikuj nazwę reguły walidacji i tekst komunikatu o błędzie (na przykład `data-val-rulename="Error message."`).
* Podaj wszelkie dodatkowe parametry wymagane przez moduł sprawdzania poprawności (na `data-val-rulename-parm1="value"`przykład).

W poniższym przykładzie przedstawiono `data-` atrybuty dla `ClassicMovie` atrybutu przykładowej aplikacji:

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

Jak wspomniano wcześniej, [pomocników tagów](xref:mvc/views/tag-helpers/intro) i [pomocników HTML](xref:mvc/views/overview) wykorzystują informacje z atrybutów walidacji do renderowania `data-` atrybutów. Istnieją dwie opcje pisania kodu, które powoduje tworzenie niestandardowych `data-` atrybutów HTML:

* Utwórz klasę, która pochodzi od `AttributeAdapterBase<TAttribute>` i klasy implementującej `IValidationAttributeAdapterProvider`, i Zarejestruj swój atrybut oraz jego kartę w programie di. Ta metoda jest zgodna z [pojedynczym podmiotem odpowiedzialnym](https://wikipedia.org/wiki/Single_responsibility_principle) w odniesieniu do kodu weryfikacyjnego związanego z serwerem i klienta jest w osobnych klasach. Adapter ma również zalety, że ponieważ jest on zarejestrowany w programie DI, w razie potrzeby są dostępne inne usługi w programie DI.
* Zaimplementuj `IClientModelValidator` w `ValidationAttribute` klasie. Ta metoda może być odpowiednia, jeśli atrybut nie wykonuje walidacji po stronie serwera i nie wymaga żadnych usług z programu DI.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter dla weryfikacji po stronie klienta

Ta metoda renderowania `data-` atrybutów w kodzie HTML jest używana przez `ClassicMovie` atrybut w przykładowej aplikacji. Aby dodać weryfikację klienta przy użyciu tej metody:

1. Utwórz klasę adaptera atrybutów dla niestandardowego atrybutu walidacji. Utwórz klasę z [\<AttributeAdapterBase T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2). Utwórz `AddValidation` metodę, która dodaje `data-` atrybuty do renderowanych danych wyjściowych, jak pokazano w tym przykładzie:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Utwórz klasę dostawcy kart implementującą <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. W `GetAttributeAdapter` metodzie Przekaż atrybut niestandardowy do konstruktora adaptera, jak pokazano w tym przykładzie:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Zarejestruj dostawcę karty dla DI in `Startup.ConfigureServices`:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator dla weryfikacji po stronie klienta

Ta metoda renderowania `data-` atrybutów w kodzie HTML jest używana przez `ClassicMovie2` atrybut w przykładowej aplikacji. Aby dodać weryfikację klienta przy użyciu tej metody:

* W atrybucie niestandardowego sprawdzania poprawności Zaimplementuj `IClientModelValidator` interfejs i Utwórz `AddValidation` metodę. W `AddValidation` metodzie Dodaj `data-` atrybuty do walidacji, jak pokazano w następującym przykładzie:

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>Wyłącz weryfikację po stronie klienta

Poniższy kod wyłącza weryfikację klienta w widokach MVC:

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

I w Razor Pages:

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

Kolejną opcją wyłączenia sprawdzania poprawności klienta jest komentarz do odwołania do `_ValidationScriptsPartial` w pliku *. cshtml* .

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przestrzeń nazw System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Powiązanie modelu](model-binding.md)

::: moniker-end
