---
title: Część 8, Dodawanie walidacji do Razor strony ASP.NET Core
author: rick-anderson
description: Część 8 serii samouczków na Razor stronach.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 86c523c69d3ee85f56bf1a51719a0bd93cbe97fc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633555"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a><span data-ttu-id="10040-103">Część 8, Dodawanie walidacji do Razor strony ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10040-103">Part 8, add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="10040-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="10040-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="10040-105">W tej sekcji logika walidacji jest dodawana do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="10040-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="10040-106">Reguły sprawdzania poprawności są wymuszane za każdym razem, gdy użytkownik tworzy lub edytuje film.</span><span class="sxs-lookup"><span data-stu-id="10040-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="10040-107">Walidacja</span><span class="sxs-lookup"><span data-stu-id="10040-107">Validation</span></span>

<span data-ttu-id="10040-108">Kluczową cechą rozwoju oprogramowania jest nazywana [sucha](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**EPEAT **Y**ourself").</span><span class="sxs-lookup"><span data-stu-id="10040-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="10040-109">Razor Strony zachęcają do programowania, w którym funkcje są określone raz i są widoczne w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="10040-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="10040-110">SUCHy może pomóc:</span><span class="sxs-lookup"><span data-stu-id="10040-110">DRY can help:</span></span>

* <span data-ttu-id="10040-111">Zmniejsz ilość kodu w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="10040-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="10040-112">Spraw, aby kod był mniej podatny na błędy i łatwiejszy do testowania i konserwowania.</span><span class="sxs-lookup"><span data-stu-id="10040-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="10040-113">Obsługa walidacji zapewniana przez Razor strony i Entity Framework jest dobrym przykładem tej zasady.</span><span class="sxs-lookup"><span data-stu-id="10040-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="10040-114">Reguły sprawdzania poprawności są deklaratywnie określone w jednym miejscu (w klasie modelu), a reguły są wymuszane wszędzie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="10040-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="10040-115">Dodawanie reguł walidacji do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="10040-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="10040-116">Przestrzeń nazw DataAnnotations zawiera zestaw wbudowanych atrybutów walidacji, które są stosowane deklaratywnie do klasy lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="10040-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="10040-117">Adnotacje DataAnnotation zawierają również atrybuty formatowania, takie jak `DataType` Pomoc dotycząca formatowania i nie zapewniają weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="10040-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="10040-118">Zaktualizuj `Movie` klasę, aby skorzystać z wbudowanych `Required` `StringLength` atrybutów,, `RegularExpression` i `Range` walidacji.</span><span class="sxs-lookup"><span data-stu-id="10040-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="10040-119">Atrybuty walidacji określają zachowanie, które chcesz wymusić na właściwościach modelu, do których są stosowane:</span><span class="sxs-lookup"><span data-stu-id="10040-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="10040-120">`Required`Atrybuty i `MinimumLength` wskazują, że właściwość musi mieć wartość, ale nic nie zapobiega wprowadzaniu przez użytkownika odstępu w celu zaspokojenia tej walidacji.</span><span class="sxs-lookup"><span data-stu-id="10040-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="10040-121">Ten `RegularExpression` atrybut służy do ograniczania, jakie znaki mogą być wprowadzane.</span><span class="sxs-lookup"><span data-stu-id="10040-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="10040-122">W poprzednim kodzie "gatunek":</span><span class="sxs-lookup"><span data-stu-id="10040-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="10040-123">Należy używać tylko liter.</span><span class="sxs-lookup"><span data-stu-id="10040-123">Must only use letters.</span></span>
  * <span data-ttu-id="10040-124">Pierwsza litera musi być wielką literą.</span><span class="sxs-lookup"><span data-stu-id="10040-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="10040-125">Odstępy, cyfry i znaki specjalne są niedozwolone.</span><span class="sxs-lookup"><span data-stu-id="10040-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="10040-126">`RegularExpression`"Ocena":</span><span class="sxs-lookup"><span data-stu-id="10040-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="10040-127">Wymaga, aby pierwszy znak był wielką literą.</span><span class="sxs-lookup"><span data-stu-id="10040-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="10040-128">Zezwala na znaki specjalne i cyfry w kolejnych odstępach.</span><span class="sxs-lookup"><span data-stu-id="10040-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="10040-129">"PG-13" jest prawidłowy dla oceny, ale kończy się niepowodzeniem dla "gatunku".</span><span class="sxs-lookup"><span data-stu-id="10040-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="10040-130">Atrybut `Range` ogranicza wartość do określonego zakresu.</span><span class="sxs-lookup"><span data-stu-id="10040-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="10040-131">Ten `StringLength` atrybut pozwala ustawić maksymalną długość właściwości ciągu i opcjonalnie jej długość minimalną.</span><span class="sxs-lookup"><span data-stu-id="10040-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="10040-132">Typy wartości (takie jak `decimal` ,,, `int` `float` `DateTime` ) są z założenia wymagane i nie wymagają `[Required]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="10040-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="10040-133">Automatyczne Wymuszanie reguł sprawdzania poprawności przez ASP.NET Core pomaga zwiększyć niezawodność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="10040-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="10040-134">Gwarantuje to również, że nie można zapomnieć, aby zweryfikować coś i przypadkowo umożliwić niewłaściwe dane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="10040-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="10040-135">Interfejs użytkownika błędu walidacji na Razor stronach</span><span class="sxs-lookup"><span data-stu-id="10040-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="10040-136">Uruchom aplikację i przejdź do stron/filmów.</span><span class="sxs-lookup"><span data-stu-id="10040-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="10040-137">Wybierz łącze **Utwórz nowy** .</span><span class="sxs-lookup"><span data-stu-id="10040-137">Select the **Create New** link.</span></span> <span data-ttu-id="10040-138">Wypełnij formularz z nieprawidłowymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="10040-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="10040-139">Gdy program jQuery po stronie klienta wykryje błąd, zostanie wyświetlony komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="10040-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formularz widoku filmu z wieloma błędami walidacji po stronie klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="10040-141">Zwróć uwagę, jak formularz automatycznie renderuje komunikat o błędzie walidacji w każdym polu zawierającym nieprawidłową wartość.</span><span class="sxs-lookup"><span data-stu-id="10040-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="10040-142">Błędy są wymuszane po stronie klienta (przy użyciu języków JavaScript i jQuery) i po stronie serwera (gdy użytkownik ma wyłączony kod JavaScript).</span><span class="sxs-lookup"><span data-stu-id="10040-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="10040-143">Znacząca korzyść polega na tym, że zmiany kodu **nie** były wymagane na stronach tworzenia i edytowania.</span><span class="sxs-lookup"><span data-stu-id="10040-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="10040-144">Gdy do modelu zastosowano adnotacje DataAnnotations, interfejs użytkownika weryfikacji został włączony.</span><span class="sxs-lookup"><span data-stu-id="10040-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="10040-145">RazorStrony utworzone w tym samouczku automatycznie pobierają reguły sprawdzania poprawności (przy użyciu atrybutów walidacji we właściwościach `Movie` klasy modelu).</span><span class="sxs-lookup"><span data-stu-id="10040-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="10040-146">Sprawdzanie poprawności testu za pomocą strony Edycja, to samo sprawdzanie poprawności jest stosowane.</span><span class="sxs-lookup"><span data-stu-id="10040-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="10040-147">Dane formularza nie są ogłaszane na serwerze, dopóki nie zostaną wykryte błędy weryfikacji po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="10040-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="10040-148">Sprawdź, czy dane formularza nie zostały ogłoszone przy użyciu co najmniej jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="10040-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="10040-149">Umieść punkt przerwania w `OnPostAsync` metodzie.</span><span class="sxs-lookup"><span data-stu-id="10040-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="10040-150">Prześlij formularz (wybierz pozycję **Utwórz** lub **Zapisz**).</span><span class="sxs-lookup"><span data-stu-id="10040-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="10040-151">Punkt przerwania nigdy nie trafi.</span><span class="sxs-lookup"><span data-stu-id="10040-151">The break point is never hit.</span></span>
* <span data-ttu-id="10040-152">Użyj [Narzędzia programu Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="10040-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="10040-153">Użyj narzędzi deweloperskich przeglądarki do monitorowania ruchu sieciowego.</span><span class="sxs-lookup"><span data-stu-id="10040-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="10040-154">Weryfikacja po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="10040-154">Server-side validation</span></span>

<span data-ttu-id="10040-155">Gdy język JavaScript jest wyłączony w przeglądarce, przesłanie formularza z błędami spowoduje opublikowanie na serwerze.</span><span class="sxs-lookup"><span data-stu-id="10040-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="10040-156">Opcjonalna, testowa weryfikacja po stronie serwera:</span><span class="sxs-lookup"><span data-stu-id="10040-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="10040-157">Wyłącz język JavaScript w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="10040-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="10040-158">Język JavaScript można wyłączyć przy użyciu narzędzi deweloperskich w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="10040-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="10040-159">Jeśli nie możesz wyłączyć języka JavaScript w przeglądarce, wypróbuj inną przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="10040-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="10040-160">Ustaw punkt przerwania w `OnPostAsync` metodzie strony Utwórz lub Edytuj.</span><span class="sxs-lookup"><span data-stu-id="10040-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="10040-161">Prześlij formularz z nieprawidłowymi danymi.</span><span class="sxs-lookup"><span data-stu-id="10040-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="10040-162">Sprawdź, czy stan modelu jest nieprawidłowy:</span><span class="sxs-lookup"><span data-stu-id="10040-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="10040-163">Alternatywnie można [wyłączyć weryfikację po stronie klienta na serwerze](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="10040-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="10040-164">Poniższy kod przedstawia część strony *Create. cshtml* podświetloną wcześniej w samouczku.</span><span class="sxs-lookup"><span data-stu-id="10040-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="10040-165">Jest on używany przez strony Tworzenie i edytowanie, aby wyświetlić początkowy formularz i ponownie wyświetlić formularz w przypadku błędu.</span><span class="sxs-lookup"><span data-stu-id="10040-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="10040-166">[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="10040-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="10040-167">[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) wyświetla błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="10040-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="10040-168">Aby uzyskać więcej informacji, zobacz [Walidacja](xref:mvc/models/validation) .</span><span class="sxs-lookup"><span data-stu-id="10040-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="10040-169">Na stronach tworzenie i edytowanie nie są dostępne żadne reguły sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="10040-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="10040-170">Reguły walidacji i ciągi błędów są określone tylko w `Movie` klasie.</span><span class="sxs-lookup"><span data-stu-id="10040-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="10040-171">Te reguły sprawdzania poprawności są automatycznie stosowane do Razor stron, które edytują `Movie` model.</span><span class="sxs-lookup"><span data-stu-id="10040-171">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="10040-172">Gdy wymagana jest zmiana logiki walidacji, jest ona wykonywana tylko w modelu.</span><span class="sxs-lookup"><span data-stu-id="10040-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="10040-173">Walidacja jest stosowana spójnie w całej aplikacji (logika walidacji jest definiowana w jednym miejscu).</span><span class="sxs-lookup"><span data-stu-id="10040-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="10040-174">Sprawdzanie poprawności w jednym miejscu pomaga zachować czysty kod i ułatwić jego utrzymywanie i aktualizowanie.</span><span class="sxs-lookup"><span data-stu-id="10040-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="10040-175">Używanie atrybutów DataType</span><span class="sxs-lookup"><span data-stu-id="10040-175">Using DataType Attributes</span></span>

<span data-ttu-id="10040-176">Zapoznaj się z `Movie` klasą.</span><span class="sxs-lookup"><span data-stu-id="10040-176">Examine the `Movie` class.</span></span> <span data-ttu-id="10040-177">`System.ComponentModel.DataAnnotations`Przestrzeń nazw zawiera atrybuty formatowania oprócz wbudowanego zestawu atrybutów walidacji.</span><span class="sxs-lookup"><span data-stu-id="10040-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="10040-178">Atrybut `DataType` jest stosowany do właściwości `ReleaseDate` i `Price`.</span><span class="sxs-lookup"><span data-stu-id="10040-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="10040-179">`DataType`Atrybuty zawierają tylko wskazówki dla aparatu widoku do formatowania danych (i udostępniają atrybuty, takie jak `<a>` adresy URL i `<a href="mailto:EmailAddress.com">` wiadomości e-mail).</span><span class="sxs-lookup"><span data-stu-id="10040-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="10040-180">Użyj `RegularExpression` atrybutu, aby sprawdzić poprawność formatu danych.</span><span class="sxs-lookup"><span data-stu-id="10040-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="10040-181">Ten `DataType` atrybut służy do określania typu danych, który jest bardziej szczegółowy niż typ wewnętrzny bazy danych.</span><span class="sxs-lookup"><span data-stu-id="10040-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="10040-182">`DataType` atrybuty nie są atrybutami walidacji.</span><span class="sxs-lookup"><span data-stu-id="10040-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="10040-183">W przykładowej aplikacji tylko data jest wyświetlana bez czasu.</span><span class="sxs-lookup"><span data-stu-id="10040-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="10040-184">`DataType`Wyliczenie zawiera wiele typów danych, takich jak data, godzina, numer telefonu, waluta, EmailAddress i inne.</span><span class="sxs-lookup"><span data-stu-id="10040-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="10040-185">Ten `DataType` atrybut może również umożliwić aplikacji automatyczne udostępnianie funkcji specyficznych dla typu.</span><span class="sxs-lookup"><span data-stu-id="10040-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="10040-186">Na przykład `mailto:` można utworzyć link dla `DataType.EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="10040-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="10040-187">`DataType.Date`W przeglądarkach, które obsługują HTML5, można podać selektor daty.</span><span class="sxs-lookup"><span data-stu-id="10040-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="10040-188">`DataType`Atrybuty emitują HTML 5 `data-` (wymawiane kreski danych), których używają przeglądarki HTML 5.</span><span class="sxs-lookup"><span data-stu-id="10040-188">The `DataType` attributes emit HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="10040-189">`DataType`Atrybuty nie zapewniają **not** żadnej weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="10040-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="10040-190">`DataType.Date` nie określa formatu wyświetlanej daty.</span><span class="sxs-lookup"><span data-stu-id="10040-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="10040-191">Domyślnie pole dane jest wyświetlane zgodnie z domyślnymi formatami opartymi na serwerze `CultureInfo` .</span><span class="sxs-lookup"><span data-stu-id="10040-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="10040-192">`[Column(TypeName = "decimal(18, 2)")]`Adnotacja danych jest wymagana, aby Entity Framework Core prawidłowo mapować `Price` do waluty w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="10040-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="10040-193">Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="10040-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="10040-194">Ten `DisplayFormat` atrybut służy do jawnego określenia formatu daty:</span><span class="sxs-lookup"><span data-stu-id="10040-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="10040-195">`ApplyFormatInEditMode`Ustawienie określa, że formatowanie ma być stosowane, gdy wartość jest wyświetlana do edycji.</span><span class="sxs-lookup"><span data-stu-id="10040-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="10040-196">Takie zachowanie może nie być konieczne w przypadku niektórych pól.</span><span class="sxs-lookup"><span data-stu-id="10040-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="10040-197">Na przykład w przypadku wartości walut prawdopodobnie nie potrzebujesz symbolu waluty w interfejsie użytkownika edycji.</span><span class="sxs-lookup"><span data-stu-id="10040-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="10040-198">Ten `DisplayFormat` atrybut może być używany przez siebie, ale zazwyczaj dobrym pomysłem jest użycie `DataType` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="10040-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="10040-199">Ten `DataType` atrybut przekazuje semantykę danych w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, których nie można uzyskać za pomocą DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="10040-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="10040-200">Przeglądarka może włączać funkcje HTML5 (na przykład w celu wyświetlania kontrolki kalendarza, symbolu waluty właściwej dla ustawień regionalnych, linków e-mail itp.).</span><span class="sxs-lookup"><span data-stu-id="10040-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="10040-201">Domyślnie przeglądarka będzie renderować dane przy użyciu poprawnego formatu na podstawie ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="10040-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="10040-202">Ten `DataType` atrybut może umożliwić usłudze ASP.NET Core Framework wybranie odpowiedniego szablonu pola w celu renderowania danych.</span><span class="sxs-lookup"><span data-stu-id="10040-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="10040-203">`DisplayFormat`, Jeśli używany przez siebie, używa szablonu ciągu.</span><span class="sxs-lookup"><span data-stu-id="10040-203">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="10040-204">**Uwaga:** Walidacja jQuery nie działa z `Range` atrybutem i `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="10040-204">**Note:** jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="10040-205">Na przykład poniższy kod zawsze będzie wyświetlał błąd walidacji po stronie klienta, nawet wtedy, gdy data jest w określonym zakresie:</span><span class="sxs-lookup"><span data-stu-id="10040-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="10040-206">Ogólnie rzecz biorąc, nie jest dobrym sposobem kompilowania dat stałych w modelach, dlatego przy użyciu `Range` atrybutu i `DateTime` nie jest to odradzane.</span><span class="sxs-lookup"><span data-stu-id="10040-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="10040-207">Poniższy kod ilustruje łączenie atrybutów w jednym wierszu:</span><span class="sxs-lookup"><span data-stu-id="10040-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="10040-208">[Wprowadzenie do Razor Na stronach i EF Core](xref:data/ef-rp/intro) są wyświetlane zaawansowane operacje EF Core z Razor stronami.</span><span class="sxs-lookup"><span data-stu-id="10040-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="10040-209">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="10040-209">Apply migrations</span></span>

<span data-ttu-id="10040-210">Adnotacje zastosowane do klasy zmieniają schemat.</span><span class="sxs-lookup"><span data-stu-id="10040-210">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="10040-211">Na przykład, adnotacje zastosowane do `Title` pola:</span><span class="sxs-lookup"><span data-stu-id="10040-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="10040-212">Ogranicza znaki do 60.</span><span class="sxs-lookup"><span data-stu-id="10040-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="10040-213">Nie zezwala na `null` wartość.</span><span class="sxs-lookup"><span data-stu-id="10040-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10040-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10040-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="10040-215">`Movie`Tabela ma obecnie następujący schemat:</span><span class="sxs-lookup"><span data-stu-id="10040-215">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="10040-216">Powyższe zmiany schematu nie powodują wygenerowania wyjątku przez EF.</span><span class="sxs-lookup"><span data-stu-id="10040-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="10040-217">Należy jednak utworzyć migrację, aby schemat był spójny z modelem.</span><span class="sxs-lookup"><span data-stu-id="10040-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="10040-218">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="10040-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="10040-219">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="10040-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="10040-220">`Update-Database` uruchamia `Up` metody `New_DataAnnotations` klasy.</span><span class="sxs-lookup"><span data-stu-id="10040-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="10040-221">Przeanalizuj metodę `Up`:</span><span class="sxs-lookup"><span data-stu-id="10040-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="10040-222">Zaktualizowana `Movie` tabela ma następujący schemat:</span><span class="sxs-lookup"><span data-stu-id="10040-222">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="10040-223">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="10040-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="10040-224">Migracja nie jest wymagana w przypadku oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="10040-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="10040-225">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="10040-225">Publish to Azure</span></span>

<span data-ttu-id="10040-226">Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji ASP.NET Core na platformie Azure przy użyciu SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="10040-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="10040-227">Dziękujemy za zakończenie tego wprowadzenia do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="10040-227">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="10040-228">[Wprowadzenie do Razor Strony i EF Core](xref:data/ef-rp/intro) są doskonałym zaobserwują się z tym samouczkiem.</span><span class="sxs-lookup"><span data-stu-id="10040-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10040-229">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="10040-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="10040-230">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="10040-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="10040-231">Poprzedni: Dodawanie nowego pola</span><span class="sxs-lookup"><span data-stu-id="10040-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
