---
title: Dodawanie sprawdzania poprawności do ASP.NET podstawowej strony razor
author: rick-anderson
description: Dowiedz się, jak dodać sprawdzanie poprawności do strony Razor w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666020"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="bcf52-103">Dodawanie sprawdzania poprawności do ASP.NET podstawowej strony razor</span><span class="sxs-lookup"><span data-stu-id="bcf52-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="bcf52-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bcf52-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bcf52-105">W tej sekcji logiki sprawdzania `Movie` poprawności jest dodawany do modelu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="bcf52-106">Reguły sprawdzania poprawności są wymuszane za każdym razem, gdy użytkownik tworzy lub edytuje film.</span><span class="sxs-lookup"><span data-stu-id="bcf52-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="bcf52-107">Walidacja</span><span class="sxs-lookup"><span data-stu-id="bcf52-107">Validation</span></span>

<span data-ttu-id="bcf52-108">Kluczowym elementem rozwoju oprogramowania jest [nazwa DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Yself").**</span><span class="sxs-lookup"><span data-stu-id="bcf52-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="bcf52-109">Razor Pages zachęca do tworzenia, gdzie funkcjonalność jest określona raz, i jest to odzwierciedlone w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bcf52-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="bcf52-110">DRY może pomóc:</span><span class="sxs-lookup"><span data-stu-id="bcf52-110">DRY can help:</span></span>

* <span data-ttu-id="bcf52-111">Zmniejsz ilość kodu w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bcf52-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="bcf52-112">Ułatw kod mniej podatne na błędy i łatwiejsze do testowania i konserwacji.</span><span class="sxs-lookup"><span data-stu-id="bcf52-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="bcf52-113">Obsługa walidacji zapewniana przez strony Razor Pages i entity framework jest dobrym przykładem zasady DRY.</span><span class="sxs-lookup"><span data-stu-id="bcf52-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="bcf52-114">Reguły sprawdzania poprawności są deklaratywnie określone w jednym miejscu (w klasie modelu), a reguły są wymuszane wszędzie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bcf52-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="bcf52-115">Dodawanie reguł sprawdzania poprawności do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="bcf52-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="bcf52-116">Obszar nazw DataAnnotations zawiera zestaw wbudowanych atrybutów sprawdzania poprawności, które są stosowane deklaratywnie do klasy lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="bcf52-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="bcf52-117">DataAnnotations zawiera również atrybuty formatowania, takie jak, `DataType` które pomagają w formatowaniu i nie zapewniają sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="bcf52-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="bcf52-118">Zaktualizuj `Movie` klasę, aby `Required`skorzystać `StringLength` `RegularExpression`z `Range` wbudowanych atrybutów , , i sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="bcf52-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="bcf52-119">Atrybuty sprawdzania poprawności określają zachowanie, które chcesz wymusić we właściwościach modelu, do których są stosowane:</span><span class="sxs-lookup"><span data-stu-id="bcf52-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="bcf52-120">`Required` Atrybuty `MinimumLength` i atrybuty wskazują, że właściwość musi mieć wartość; ale nic nie stoi na przeszkodzie, aby użytkownik wejdząc do białego miejsca, aby spełnić tę walidację.</span><span class="sxs-lookup"><span data-stu-id="bcf52-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="bcf52-121">Atrybut `RegularExpression` jest używany do ograniczania, jakie znaki mogą być wprowadzane.</span><span class="sxs-lookup"><span data-stu-id="bcf52-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="bcf52-122">W poprzednim kodzie "Genre":</span><span class="sxs-lookup"><span data-stu-id="bcf52-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="bcf52-123">Może używać tylko liter.</span><span class="sxs-lookup"><span data-stu-id="bcf52-123">Must only use letters.</span></span>
  * <span data-ttu-id="bcf52-124">Pierwsza litera musi być wielką literą.</span><span class="sxs-lookup"><span data-stu-id="bcf52-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="bcf52-125">Białe znaki, liczby i znaki specjalne nie są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="bcf52-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="bcf52-126">"Ocena": `RegularExpression`</span><span class="sxs-lookup"><span data-stu-id="bcf52-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="bcf52-127">Wymaga, aby pierwszy znak był wielką literą.</span><span class="sxs-lookup"><span data-stu-id="bcf52-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="bcf52-128">Umożliwia znaki specjalne i liczby w kolejnych spacjach.</span><span class="sxs-lookup"><span data-stu-id="bcf52-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="bcf52-129">"PG-13" jest ważny dla oceny, ale nie dla "Gatunku".</span><span class="sxs-lookup"><span data-stu-id="bcf52-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="bcf52-130">Atrybut `Range` ogranicza wartość do określonego zakresu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="bcf52-131">Atrybut `StringLength` umożliwia ustawienie maksymalnej długości właściwości ciągu i opcjonalnie jego minimalnej długości.</span><span class="sxs-lookup"><span data-stu-id="bcf52-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="bcf52-132">Typy wartości `decimal`(takie `float` `DateTime`jak , `int`, , ) są `[Required]` z natury wymagane i nie potrzebują atrybutu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="bcf52-133">Automatyczne wymuszanie reguł sprawdzania poprawności przez ASP.NET Core pomaga uczynić aplikację bardziej niezawodną.</span><span class="sxs-lookup"><span data-stu-id="bcf52-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="bcf52-134">Zapewnia również, że nie można zapomnieć, aby sprawdzić poprawność czegoś i przypadkowo wpuścić złe dane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bcf52-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="bcf52-135">Interfejs użytkownika błędu sprawdzania poprawności na stronach Razor</span><span class="sxs-lookup"><span data-stu-id="bcf52-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="bcf52-136">Uruchom aplikację i przejdź do strony/filmy.</span><span class="sxs-lookup"><span data-stu-id="bcf52-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="bcf52-137">Wybierz łącze **Utwórz nowy.**</span><span class="sxs-lookup"><span data-stu-id="bcf52-137">Select the **Create New** link.</span></span> <span data-ttu-id="bcf52-138">Wypełnij formularz z nieprawidłowymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="bcf52-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="bcf52-139">Gdy sprawdzanie poprawności po stronie klienta jQuery wykryje błąd, wyświetla komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="bcf52-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formularz widoku filmu z wieloma błędami sprawdzania poprawności po stronie klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="bcf52-141">Zwróć uwagę, jak formularz automatycznie renderował komunikat o błędzie sprawdzania poprawności w każdym polu zawierającym nieprawidłową wartość.</span><span class="sxs-lookup"><span data-stu-id="bcf52-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="bcf52-142">Błędy są wymuszane zarówno po stronie klienta (przy użyciu Języka JavaScript i jQuery) i po stronie serwera (gdy użytkownik ma wyłączony javascript).</span><span class="sxs-lookup"><span data-stu-id="bcf52-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="bcf52-143">Istotną korzyścią jest to, że na stronach Tworzenie lub edytowanie nie były wymagane **żadne** zmiany kodu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="bcf52-144">Po zastosowaniu dataannotations do modelu, sprawdzanie poprawności interfejsu użytkownika została włączona.</span><span class="sxs-lookup"><span data-stu-id="bcf52-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="bcf52-145">Strony Razor utworzone w tym samouczku automatycznie podniósł reguły sprawdzania poprawności `Movie` (przy użyciu atrybutów sprawdzania poprawności właściwości klasy modelu).</span><span class="sxs-lookup"><span data-stu-id="bcf52-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="bcf52-146">Sprawdzanie poprawności testu przy użyciu strony Edytuj, ta sama weryfikacja jest stosowana.</span><span class="sxs-lookup"><span data-stu-id="bcf52-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="bcf52-147">Dane formularza nie są księgowane na serwerze, dopóki nie ma żadnych błędów sprawdzania poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="bcf52-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="bcf52-148">Sprawdź, czy dane formularza nie są publikowane przez co najmniej jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="bcf52-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="bcf52-149">Umieść punkt przerwania `OnPostAsync` w metodzie.</span><span class="sxs-lookup"><span data-stu-id="bcf52-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="bcf52-150">Prześlij formularz (wybierz **utwórz** lub **zapisz).**</span><span class="sxs-lookup"><span data-stu-id="bcf52-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="bcf52-151">Break point nigdy nie zostanie trafiony.</span><span class="sxs-lookup"><span data-stu-id="bcf52-151">The break point is never hit.</span></span>
* <span data-ttu-id="bcf52-152">Użyj [narzędzia Skrzypek](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="bcf52-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="bcf52-153">Narzędzia programistyczne przeglądarki są używane do monitorowania ruchu sieciowego.</span><span class="sxs-lookup"><span data-stu-id="bcf52-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="bcf52-154">Sprawdzanie poprawności po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="bcf52-154">Server-side validation</span></span>

<span data-ttu-id="bcf52-155">Gdy JavaScript jest wyłączony w przeglądarce, przesłanie formularza z błędami zostanie zaksięgowany na serwerze.</span><span class="sxs-lookup"><span data-stu-id="bcf52-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="bcf52-156">Opcjonalne sprawdzanie poprawności po stronie serwera:</span><span class="sxs-lookup"><span data-stu-id="bcf52-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="bcf52-157">Wyłącz obsługę JavaScript w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="bcf52-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="bcf52-158">Możesz wyłączyć JavaScript za pomocą narzędzi programistycznych przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="bcf52-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="bcf52-159">Jeśli nie możesz wyłączyć javascriptu w przeglądarce, spróbuj użyć innej przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="bcf52-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="bcf52-160">Ustaw punkt przerwania `OnPostAsync` w metodzie strony Utwórz lub Edytuj.</span><span class="sxs-lookup"><span data-stu-id="bcf52-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="bcf52-161">Prześlij formularz z nieprawidłowymi danymi.</span><span class="sxs-lookup"><span data-stu-id="bcf52-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="bcf52-162">Sprawdź, czy stan modelu jest nieprawidłowy:</span><span class="sxs-lookup"><span data-stu-id="bcf52-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="bcf52-163">Alternatywnie można [wyłączyć sprawdzanie poprawności po stronie klienta na serwerze](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="bcf52-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="bcf52-164">Poniższy kod przedstawia część *Create.cshtml* strony szkieletu wcześniej w samouczku.</span><span class="sxs-lookup"><span data-stu-id="bcf52-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="bcf52-165">Jest on używany przez strony Tworzenie i edytowanie do wyświetlania formularza początkowego i ponownego wyświetlania formularza w przypadku błędu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="bcf52-166">[Pomocnik znacznika wejściowego](xref:mvc/views/working-with-forms) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML potrzebne do sprawdzania poprawności jQuery po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="bcf52-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="bcf52-167">[Pomocnik tagu sprawdzania poprawności](xref:mvc/views/working-with-forms#the-validation-tag-helpers) wyświetla błędy sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="bcf52-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="bcf52-168">Zobacz [Sprawdzanie poprawności,](xref:mvc/models/validation) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="bcf52-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="bcf52-169">Strony Tworzenie i edytowanie nie mają w nich reguł sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="bcf52-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="bcf52-170">Reguły sprawdzania poprawności i ciągi błędów są określone tylko w `Movie` klasie.</span><span class="sxs-lookup"><span data-stu-id="bcf52-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="bcf52-171">Te reguły sprawdzania poprawności są automatycznie stosowane `Movie` do stron Razor, które edytują model.</span><span class="sxs-lookup"><span data-stu-id="bcf52-171">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="bcf52-172">Gdy logika sprawdzania poprawności musi się zmienić, odbywa się tylko w modelu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="bcf52-173">Sprawdzanie poprawności jest stosowane konsekwentnie w całej aplikacji (logika sprawdzania poprawności jest zdefiniowana w jednym miejscu).</span><span class="sxs-lookup"><span data-stu-id="bcf52-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="bcf52-174">Sprawdzanie poprawności w jednym miejscu pomaga utrzymać kod w czystości i ułatwia utrzymanie i aktualizację.</span><span class="sxs-lookup"><span data-stu-id="bcf52-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="bcf52-175">Korzystanie z atrybutów datatype</span><span class="sxs-lookup"><span data-stu-id="bcf52-175">Using DataType Attributes</span></span>

<span data-ttu-id="bcf52-176">Sprawdź `Movie` klasę.</span><span class="sxs-lookup"><span data-stu-id="bcf52-176">Examine the `Movie` class.</span></span> <span data-ttu-id="bcf52-177">Obszar `System.ComponentModel.DataAnnotations` nazw udostępnia atrybuty formatowania oprócz wbudowanego zestawu atrybutów sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="bcf52-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="bcf52-178">Atrybut `DataType` jest stosowany do właściwości `ReleaseDate` i `Price`.</span><span class="sxs-lookup"><span data-stu-id="bcf52-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="bcf52-179">Atrybuty `DataType` zawierają tylko wskazówki dla aparatu widoku, aby sformatować dane (i dostarcza atrybuty, takie jak `<a>` adres URL i `<a href="mailto:EmailAddress.com">` dla poczty e-mail).</span><span class="sxs-lookup"><span data-stu-id="bcf52-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="bcf52-180">Użyj `RegularExpression` atrybutu, aby sprawdzić poprawność formatu danych.</span><span class="sxs-lookup"><span data-stu-id="bcf52-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="bcf52-181">Atrybut `DataType` jest używany do określenia typu danych, który jest bardziej szczegółowy niż typ wewnętrznego bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bcf52-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="bcf52-182">`DataType`atrybuty nie są atrybutami sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="bcf52-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="bcf52-183">W przykładowej aplikacji wyświetlana jest tylko data bez czasu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="bcf52-184">Wyliczenie `DataType` zawiera wiele typów danych, takich jak Data, Godzina, Numer telefonu, Waluta, EmailAddress i inne.</span><span class="sxs-lookup"><span data-stu-id="bcf52-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="bcf52-185">Atrybut `DataType` może również włączyć aplikację, aby automatycznie udostępniać funkcje specyficzne dla typu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="bcf52-186">Na przykład `mailto:` można utworzyć łącze dla pliku `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="bcf52-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="bcf52-187">Selektor daty może być `DataType.Date` podany w przeglądarkach obsługujących html5.</span><span class="sxs-lookup"><span data-stu-id="bcf52-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="bcf52-188">Atrybuty `DataType` emitują atrybuty HTML 5 `data-` (wymawiane rozdzielenie danych), które zużywają przeglądarki HTML 5.</span><span class="sxs-lookup"><span data-stu-id="bcf52-188">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="bcf52-189">Atrybuty `DataType` **nie** zapewniają sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="bcf52-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="bcf52-190">`DataType.Date`nie określa formatu daty, która jest wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="bcf52-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="bcf52-191">Domyślnie pole danych jest wyświetlane zgodnie z domyślnymi formatami `CultureInfo`opartymi na serwerze .</span><span class="sxs-lookup"><span data-stu-id="bcf52-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="bcf52-192">Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych jest wymagana, aby entity `Price` Framework Core można poprawnie mapować do waluty w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bcf52-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="bcf52-193">Aby uzyskać więcej informacji, zobacz [Typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="bcf52-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="bcf52-194">Atrybut `DisplayFormat` jest używany do jawnego określenia formatu daty:</span><span class="sxs-lookup"><span data-stu-id="bcf52-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="bcf52-195">Ustawienie `ApplyFormatInEditMode` określa, że formatowanie powinno być stosowane, gdy wartość jest wyświetlana do edycji.</span><span class="sxs-lookup"><span data-stu-id="bcf52-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="bcf52-196">Możesz nie chcieć tego zachowania dla niektórych pól.</span><span class="sxs-lookup"><span data-stu-id="bcf52-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="bcf52-197">Na przykład w wartościach walutowych prawdopodobnie symbol waluty w interfejsie użytkownika edycji jest prawdopodobnie nieokiedy.</span><span class="sxs-lookup"><span data-stu-id="bcf52-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="bcf52-198">Atrybut `DisplayFormat` może być używany przez siebie, ale ogólnie jest to `DataType` dobry pomysł, aby użyć atrybutu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="bcf52-199">Atrybut `DataType` przekazuje semantykę danych, w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, które nie są uchybienia z DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="bcf52-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="bcf52-200">Przeglądarka może włączyć funkcje HTML5 (na przykład, aby wyświetlić formant kalendarza, symbol waluty odpowiednie dla ustawień regionalnych, linki e-mail, itp.)</span><span class="sxs-lookup"><span data-stu-id="bcf52-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="bcf52-201">Domyślnie przeglądarka będzie renderować dane przy użyciu odpowiedniego formatu na podstawie ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="bcf52-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="bcf52-202">Atrybut `DataType` może włączyć ASP.NET Core framework, aby wybrać odpowiedni szablon pola do renderowania danych.</span><span class="sxs-lookup"><span data-stu-id="bcf52-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="bcf52-203">Jeśli `DisplayFormat` jest używany przez siebie używa szablonu ciągu.</span><span class="sxs-lookup"><span data-stu-id="bcf52-203">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="bcf52-204">Uwaga: sprawdzanie poprawności jQuery nie `Range` działa `DateTime`z atrybutem i .</span><span class="sxs-lookup"><span data-stu-id="bcf52-204">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="bcf52-205">Na przykład następujący kod zawsze będzie wyświetlać błąd sprawdzania poprawności po stronie klienta, nawet jeśli data znajduje się w określonym zakresie:</span><span class="sxs-lookup"><span data-stu-id="bcf52-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="bcf52-206">Zazwyczaj nie jest dobrą praktyką do kompilowania twardych dat `Range` w `DateTime` modelach, więc przy użyciu atrybutu i jest odradzane.</span><span class="sxs-lookup"><span data-stu-id="bcf52-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="bcf52-207">Poniższy kod pokazuje łączenie atrybutów w jednym wierszu:</span><span class="sxs-lookup"><span data-stu-id="bcf52-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="bcf52-208">[Wprowadzenie do stron Razor i EF Core](xref:data/ef-rp/intro) pokazuje zaawansowane operacje EF Core ze stronami Razor.</span><span class="sxs-lookup"><span data-stu-id="bcf52-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="bcf52-209">Stosowanie migracji</span><span class="sxs-lookup"><span data-stu-id="bcf52-209">Apply migrations</span></span>

<span data-ttu-id="bcf52-210">DataAnnotations zastosowane do klasy zmienić schemat.</span><span class="sxs-lookup"><span data-stu-id="bcf52-210">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="bcf52-211">Na przykład dataAnnotations stosowane do `Title` pola:</span><span class="sxs-lookup"><span data-stu-id="bcf52-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="bcf52-212">Ogranicza znaki do 60.</span><span class="sxs-lookup"><span data-stu-id="bcf52-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="bcf52-213">Nie zezwala na `null` wartość.</span><span class="sxs-lookup"><span data-stu-id="bcf52-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bcf52-214">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bcf52-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bcf52-215">Tabela `Movie` ma obecnie następujący schemat:</span><span class="sxs-lookup"><span data-stu-id="bcf52-215">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="bcf52-216">Poprzednie zmiany schematu nie powodują EF zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="bcf52-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="bcf52-217">Jednak utworzyć migrację, więc schemat jest zgodny z modelem.</span><span class="sxs-lookup"><span data-stu-id="bcf52-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="bcf52-218">Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="bcf52-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="bcf52-219">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="bcf52-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="bcf52-220">`Update-Database`uruchamia `Up` metody `New_DataAnnotations` klasy.</span><span class="sxs-lookup"><span data-stu-id="bcf52-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="bcf52-221">Zbadaj `Up` metodę:</span><span class="sxs-lookup"><span data-stu-id="bcf52-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="bcf52-222">Zaktualizowana `Movie` tabela ma następujący schemat:</span><span class="sxs-lookup"><span data-stu-id="bcf52-222">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bcf52-223">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="bcf52-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bcf52-224">Migracje nie są wymagane dla SQLite.</span><span class="sxs-lookup"><span data-stu-id="bcf52-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="bcf52-225">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="bcf52-225">Publish to Azure</span></span>

<span data-ttu-id="bcf52-226">Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji ASP.NET Core na platformie Azure za pomocą bazy danych SQL.](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)</span><span class="sxs-lookup"><span data-stu-id="bcf52-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="bcf52-227">Dziękujemy za ukończenie tego wprowadzenia do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="bcf52-227">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="bcf52-228">[Zacznij ować rezygnować Razor Strony i EF Rdzeń](xref:data/ef-rp/intro) jest an an znakomity następować po ten wychowawczy.</span><span class="sxs-lookup"><span data-stu-id="bcf52-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bcf52-229">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bcf52-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="bcf52-230">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="bcf52-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="bcf52-231">Poprzedni: Dodawanie nowego pola</span><span class="sxs-lookup"><span data-stu-id="bcf52-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
