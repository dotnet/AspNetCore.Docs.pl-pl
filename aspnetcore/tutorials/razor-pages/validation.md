---
title: Część 8, Dodawanie walidacji
author: rick-anderson
description: 'Część 8 serii samouczków na :::no-loc(Razor)::: stronach.'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/validation
ms.openlocfilehash: efae7d79ff7a0b351afc68264463546bb26b4424
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422707"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a><span data-ttu-id="674c7-103">Część 8, Dodawanie walidacji do :::no-loc(Razor)::: strony ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="674c7-103">Part 8, add validation to an ASP.NET Core :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="674c7-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="674c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="674c7-105">W tej sekcji logika walidacji jest dodawana do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="674c7-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="674c7-106">Reguły sprawdzania poprawności są wymuszane za każdym razem, gdy użytkownik tworzy lub edytuje film.</span><span class="sxs-lookup"><span data-stu-id="674c7-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="674c7-107">Walidacja</span><span class="sxs-lookup"><span data-stu-id="674c7-107">Validation</span></span>

<span data-ttu-id="674c7-108">Kluczową cechą rozwoju oprogramowania jest nazywana [sucha](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** EPEAT **Y** ourself").</span><span class="sxs-lookup"><span data-stu-id="674c7-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="674c7-109">:::no-loc(Razor)::: Strony zachęcają do programowania, w którym funkcje są określone raz i są widoczne w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-109">:::no-loc(Razor)::: Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="674c7-110">SUCHy może pomóc:</span><span class="sxs-lookup"><span data-stu-id="674c7-110">DRY can help:</span></span>

* <span data-ttu-id="674c7-111">Zmniejsz ilość kodu w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="674c7-112">Spraw, aby kod był mniej podatny na błędy i łatwiejszy do testowania i konserwowania.</span><span class="sxs-lookup"><span data-stu-id="674c7-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="674c7-113">Obsługa walidacji zapewniana przez :::no-loc(Razor)::: strony i Entity Framework jest dobrym przykładem zasady sucha:</span><span class="sxs-lookup"><span data-stu-id="674c7-113">The validation support provided by :::no-loc(Razor)::: Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="674c7-114">Reguły sprawdzania poprawności są deklaratywnie określone w jednym miejscu, w klasie model.</span><span class="sxs-lookup"><span data-stu-id="674c7-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="674c7-115">Reguły są wymuszane wszędzie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="674c7-116">Dodawanie reguł walidacji do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="674c7-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="674c7-117">`DataAnnotations`Przestrzeń nazw zawiera:</span><span class="sxs-lookup"><span data-stu-id="674c7-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="674c7-118">Zestaw wbudowanych atrybutów walidacji, które są stosowane deklaratywnie do klasy lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="674c7-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="674c7-119">Atrybuty formatowania podobne do `[DataType]` formatowania i nie zapewniają weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="674c7-120">Zaktualizuj `Movie` klasę, aby skorzystać z wbudowanych `[Required]` `[StringLength]` atrybutów,, `[RegularExpression]` i `[Range]` walidacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="674c7-121">Atrybuty walidacji określają zachowanie do wymuszania na właściwościach modelu, do których są stosowane:</span><span class="sxs-lookup"><span data-stu-id="674c7-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="674c7-122">`[Required]`Atrybuty i `[MinimumLength]` wskazują, że właściwość musi mieć wartość.</span><span class="sxs-lookup"><span data-stu-id="674c7-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="674c7-123">Nic nie zapobiega wprowadzaniu przez użytkownika białych znaków w celu zaspokojenia tej walidacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="674c7-124">Ten `[RegularExpression]` atrybut służy do ograniczania, jakie znaki mogą być wprowadzane.</span><span class="sxs-lookup"><span data-stu-id="674c7-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="674c7-125">W powyższym kodzie `Genre` :</span><span class="sxs-lookup"><span data-stu-id="674c7-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="674c7-126">Należy używać tylko liter.</span><span class="sxs-lookup"><span data-stu-id="674c7-126">Must only use letters.</span></span>
  * <span data-ttu-id="674c7-127">Pierwsza litera musi być wielką literą.</span><span class="sxs-lookup"><span data-stu-id="674c7-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="674c7-128">Odstępy, cyfry i znaki specjalne są niedozwolone.</span><span class="sxs-lookup"><span data-stu-id="674c7-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="674c7-129">`RegularExpression` `Rating` :</span><span class="sxs-lookup"><span data-stu-id="674c7-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="674c7-130">Wymaga, aby pierwszy znak był wielką literą.</span><span class="sxs-lookup"><span data-stu-id="674c7-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="674c7-131">Zezwala na znaki specjalne i cyfry w kolejnych odstępach.</span><span class="sxs-lookup"><span data-stu-id="674c7-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="674c7-132">"PG-13" jest prawidłowy dla oceny, ale kończy się niepowodzeniem `Genre` .</span><span class="sxs-lookup"><span data-stu-id="674c7-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="674c7-133">Atrybut `[Range]` ogranicza wartość do określonego zakresu.</span><span class="sxs-lookup"><span data-stu-id="674c7-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="674c7-134">`[StringLength]`Atrybut może ustawiać maksymalną długość właściwości ciągu i opcjonalnie jej długość minimalną.</span><span class="sxs-lookup"><span data-stu-id="674c7-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="674c7-135">Typy wartości, takie jak,,,, `decimal` `int` są z `float` `DateTime` założenia wymagane i nie potrzebują `[Required]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="674c7-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="674c7-136">Poprzednie reguły walidacji są używane w celu pokazania, ale nie są optymalne dla systemu produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="674c7-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="674c7-137">Na przykład powyższe zapobiega wprowadzaniu filmu z tylko dwoma znakami i nie zezwala na znaki specjalne w `Genre` .</span><span class="sxs-lookup"><span data-stu-id="674c7-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="674c7-138">Automatyczne Wymuszanie reguł sprawdzania poprawności przez ASP.NET Core ułatwia:</span><span class="sxs-lookup"><span data-stu-id="674c7-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="674c7-139">Pomaga zwiększyć niezawodność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="674c7-140">Zmniejsz prawdopodobieństwo zapisania nieprawidłowych danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="674c7-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="674c7-141">Interfejs użytkownika błędu walidacji na :::no-loc(Razor)::: stronach</span><span class="sxs-lookup"><span data-stu-id="674c7-141">Validation Error UI in :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="674c7-142">Uruchom aplikację i przejdź do stron/filmów.</span><span class="sxs-lookup"><span data-stu-id="674c7-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="674c7-143">Wybierz **:::no-loc(Create)::: Nowy** link.</span><span class="sxs-lookup"><span data-stu-id="674c7-143">Select the **:::no-loc(Create)::: New** link.</span></span> <span data-ttu-id="674c7-144">Wypełnij formularz z nieprawidłowymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="674c7-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="674c7-145">Gdy program jQuery po stronie klienta wykryje błąd, zostanie wyświetlony komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="674c7-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formularz widoku filmu z wieloma błędami walidacji po stronie klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="674c7-147">Zwróć uwagę, jak formularz automatycznie renderuje komunikat o błędzie walidacji w każdym polu zawierającym nieprawidłową wartość.</span><span class="sxs-lookup"><span data-stu-id="674c7-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="674c7-148">Błędy są wymuszane po stronie klienta, przy użyciu języków JavaScript i jQuery oraz po stronie serwera, gdy użytkownik ma wyłączone JavaScript.</span><span class="sxs-lookup"><span data-stu-id="674c7-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="674c7-149">Znacząca korzyść polega na tym, że żadne zmiany w kodzie **nie** były wymagane na :::no-loc(Create)::: stronach ani w edycji.</span><span class="sxs-lookup"><span data-stu-id="674c7-149">A significant benefit is that **no** code changes were necessary in the :::no-loc(Create)::: or Edit pages.</span></span> <span data-ttu-id="674c7-150">Po zastosowaniu adnotacji danych do modelu interfejs użytkownika weryfikacji został włączony.</span><span class="sxs-lookup"><span data-stu-id="674c7-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="674c7-151">:::no-loc(Razor):::Strony utworzone w tym samouczku automatycznie pobierają reguły sprawdzania poprawności, używając atrybutów walidacji we właściwościach `Movie` klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="674c7-151">The :::no-loc(Razor)::: Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="674c7-152">Sprawdzanie poprawności testu za pomocą strony Edycja, to samo sprawdzanie poprawności jest stosowane.</span><span class="sxs-lookup"><span data-stu-id="674c7-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="674c7-153">Dane formularza nie są ogłaszane na serwerze, dopóki nie zostaną wykryte błędy weryfikacji po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="674c7-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="674c7-154">Sprawdź, czy dane formularza nie zostały ogłoszone przy użyciu co najmniej jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="674c7-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="674c7-155">Umieść punkt przerwania w `OnPostAsync` metodzie.</span><span class="sxs-lookup"><span data-stu-id="674c7-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="674c7-156">Prześlij formularz, wybierając **:::no-loc(Create):::** lub **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="674c7-156">Submit the form by selecting **:::no-loc(Create):::** or **Save**.</span></span> <span data-ttu-id="674c7-157">Punkt przerwania nigdy nie trafi.</span><span class="sxs-lookup"><span data-stu-id="674c7-157">The break point is never hit.</span></span>
* <span data-ttu-id="674c7-158">Użyj [Narzędzia programu Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="674c7-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="674c7-159">Użyj narzędzi deweloperskich przeglądarki do monitorowania ruchu sieciowego.</span><span class="sxs-lookup"><span data-stu-id="674c7-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="674c7-160">Weryfikacja po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="674c7-160">Server-side validation</span></span>

<span data-ttu-id="674c7-161">Gdy język JavaScript jest wyłączony w przeglądarce, przesłanie formularza z błędami spowoduje opublikowanie na serwerze.</span><span class="sxs-lookup"><span data-stu-id="674c7-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="674c7-162">Opcjonalna, testowa weryfikacja po stronie serwera:</span><span class="sxs-lookup"><span data-stu-id="674c7-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="674c7-163">Wyłącz język JavaScript w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="674c7-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="674c7-164">Język JavaScript można wyłączyć za pomocą narzędzi deweloperskich przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="674c7-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="674c7-165">Jeśli nie można wyłączyć języka JavaScript w przeglądarce, wypróbuj inną przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="674c7-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="674c7-166">Ustaw punkt przerwania w `OnPostAsync` metodzie lub na :::no-loc(Create)::: stronie edycji.</span><span class="sxs-lookup"><span data-stu-id="674c7-166">Set a break point in the `OnPostAsync` method of the :::no-loc(Create)::: or Edit page.</span></span>
1. <span data-ttu-id="674c7-167">Prześlij formularz z nieprawidłowymi danymi.</span><span class="sxs-lookup"><span data-stu-id="674c7-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="674c7-168">Sprawdź, czy stan modelu jest nieprawidłowy:</span><span class="sxs-lookup"><span data-stu-id="674c7-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="674c7-169">Alternatywnie można [wyłączyć weryfikację po stronie klienta na serwerze](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="674c7-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="674c7-170">Poniższy kod przedstawia część strony *:::no-loc(Create)::: . cshtml* podświetloną we wcześniejszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="674c7-170">The following code shows a portion of the *:::no-loc(Create):::.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="674c7-171">Jest on używany przez :::no-loc(Create)::: i edytuje strony do:</span><span class="sxs-lookup"><span data-stu-id="674c7-171">It's used by the :::no-loc(Create)::: and Edit pages to:</span></span>

* <span data-ttu-id="674c7-172">Wyświetlanie formularza początkowego.</span><span class="sxs-lookup"><span data-stu-id="674c7-172">Display the initial form.</span></span>
* <span data-ttu-id="674c7-173">Ponownie Wyświetl formularz w przypadku błędu.</span><span class="sxs-lookup"><span data-stu-id="674c7-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml?range=14-20)]

<span data-ttu-id="674c7-174">[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="674c7-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="674c7-175">[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) wyświetla błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="674c7-176">Aby uzyskać więcej informacji, zobacz [Walidacja](xref:mvc/models/validation) .</span><span class="sxs-lookup"><span data-stu-id="674c7-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="674c7-177">:::no-loc(Create):::Strony edycji i nie mają żadnych reguł walidacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-177">The :::no-loc(Create)::: and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="674c7-178">Reguły walidacji i ciągi błędów są określone tylko w `Movie` klasie.</span><span class="sxs-lookup"><span data-stu-id="674c7-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="674c7-179">Te reguły sprawdzania poprawności są automatycznie stosowane do :::no-loc(Razor)::: stron, które edytują `Movie` model.</span><span class="sxs-lookup"><span data-stu-id="674c7-179">These validation rules are automatically applied to :::no-loc(Razor)::: Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="674c7-180">Gdy wymagana jest zmiana logiki walidacji, jest ona wykonywana tylko w modelu.</span><span class="sxs-lookup"><span data-stu-id="674c7-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="674c7-181">Walidacja jest stosowana spójnie w całej aplikacji. Logika walidacji jest definiowana w jednym miejscu.</span><span class="sxs-lookup"><span data-stu-id="674c7-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="674c7-182">Sprawdzanie poprawności w jednym miejscu pomaga zachować czysty kod i ułatwić jego utrzymywanie i aktualizowanie.</span><span class="sxs-lookup"><span data-stu-id="674c7-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="674c7-183">Używanie atrybutów DataType</span><span class="sxs-lookup"><span data-stu-id="674c7-183">Use DataType Attributes</span></span>

<span data-ttu-id="674c7-184">Zapoznaj się z `Movie` klasą.</span><span class="sxs-lookup"><span data-stu-id="674c7-184">Examine the `Movie` class.</span></span> <span data-ttu-id="674c7-185">`System.ComponentModel.DataAnnotations`Przestrzeń nazw zawiera atrybuty formatowania oprócz wbudowanego zestawu atrybutów walidacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="674c7-186">Atrybut `[DataType]` jest stosowany do właściwości `ReleaseDate` i `Price`.</span><span class="sxs-lookup"><span data-stu-id="674c7-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="674c7-187">`[DataType]`Atrybuty zapewniają:</span><span class="sxs-lookup"><span data-stu-id="674c7-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="674c7-188">Wskazówki dotyczące aparatu widoku do formatowania danych.</span><span class="sxs-lookup"><span data-stu-id="674c7-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="674c7-189">Dostarcza atrybuty, takie jak `<a>` adresy URL i `<a href="mailto:EmailAddress.com">` wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="674c7-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="674c7-190">Użyj `[RegularExpression]` atrybutu, aby sprawdzić poprawność formatu danych.</span><span class="sxs-lookup"><span data-stu-id="674c7-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="674c7-191">Ten `[DataType]` atrybut służy do określania typu danych, który jest bardziej szczegółowy niż typ wewnętrzny bazy danych.</span><span class="sxs-lookup"><span data-stu-id="674c7-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="674c7-192">`[DataType]` atrybuty nie są atrybutami walidacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="674c7-193">W przykładowej aplikacji tylko data jest wyświetlana bez czasu.</span><span class="sxs-lookup"><span data-stu-id="674c7-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="674c7-194">`DataType`Wyliczenie zawiera wiele typów danych, takich jak `Date` , `Time` ,,, i nie `PhoneNumber` `Currency` `EmailAddress` tylko.</span><span class="sxs-lookup"><span data-stu-id="674c7-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="674c7-195">`[DataType]`Atrybuty:</span><span class="sxs-lookup"><span data-stu-id="674c7-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="674c7-196">Może umożliwić aplikacji automatyczne udostępnianie funkcji specyficznych dla typu.</span><span class="sxs-lookup"><span data-stu-id="674c7-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="674c7-197">Na przykład `mailto:` można utworzyć link dla `DataType.EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="674c7-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="674c7-198">Może udostępniać selektor daty `DataType.Date` w przeglądarkach, które obsługują HTML5.</span><span class="sxs-lookup"><span data-stu-id="674c7-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="674c7-199">Emituje kod HTML 5 `data-` , wymawiany "kreska danych", atrybuty zużywane przez przeglądarki HTML 5.</span><span class="sxs-lookup"><span data-stu-id="674c7-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="674c7-200">**Nie** należy podawać żadnej weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="674c7-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="674c7-201">`DataType.Date` nie określa formatu wyświetlanej daty.</span><span class="sxs-lookup"><span data-stu-id="674c7-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="674c7-202">Domyślnie pole dane jest wyświetlane zgodnie z domyślnymi formatami opartymi na serwerze `CultureInfo` .</span><span class="sxs-lookup"><span data-stu-id="674c7-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="674c7-203">`[Column(TypeName = "decimal(18, 2)")]`Adnotacja danych jest wymagana, aby Entity Framework Core prawidłowo mapować `Price` do waluty w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="674c7-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="674c7-204">Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="674c7-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="674c7-205">Ten `[DisplayFormat]` atrybut służy do jawnego określenia formatu daty:</span><span class="sxs-lookup"><span data-stu-id="674c7-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="674c7-206">`ApplyFormatInEditMode`Ustawienie określa, że formatowanie zostanie zastosowane, gdy wartość zostanie wyświetlona do edycji.</span><span class="sxs-lookup"><span data-stu-id="674c7-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="674c7-207">Takie zachowanie może nie być potrzebne w przypadku niektórych pól.</span><span class="sxs-lookup"><span data-stu-id="674c7-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="674c7-208">Na przykład w przypadku wartości walut symbol waluty zwykle nie jest wymagany w interfejsie użytkownika edycji.</span><span class="sxs-lookup"><span data-stu-id="674c7-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="674c7-209">Ten `[DisplayFormat]` atrybut może być używany przez siebie, ale zazwyczaj dobrym pomysłem jest użycie `[DataType]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="674c7-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="674c7-210">Ten `[DataType]` atrybut przekazuje semantykę danych w przeciwieństwie do sposobu renderowania na ekranie.</span><span class="sxs-lookup"><span data-stu-id="674c7-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="674c7-211">Ten `[DataType]` atrybut zapewnia następujące korzyści, które nie są dostępne w programie `[DisplayFormat]` :</span><span class="sxs-lookup"><span data-stu-id="674c7-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="674c7-212">Przeglądarka może włączyć funkcje HTML5, na przykład wyświetlić kontrolkę kalendarz, symbol waluty odpowiedni dla ustawień regionalnych, linki e-mail itp.</span><span class="sxs-lookup"><span data-stu-id="674c7-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="674c7-213">Domyślnie przeglądarka renderuje dane przy użyciu poprawnego formatu na podstawie jego ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="674c7-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="674c7-214">Ten `[DataType]` atrybut może umożliwić usłudze ASP.NET Core Framework wybranie odpowiedniego szablonu pola w celu renderowania danych.</span><span class="sxs-lookup"><span data-stu-id="674c7-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="674c7-215">`DisplayFormat`, Jeśli używany przez siebie, używa szablonu ciągu.</span><span class="sxs-lookup"><span data-stu-id="674c7-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="674c7-216">**Uwaga:** Walidacja jQuery nie działa z `[Range]` atrybutem i `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="674c7-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="674c7-217">Na przykład poniższy kod zawsze będzie wyświetlał błąd walidacji po stronie klienta, nawet wtedy, gdy data jest w określonym zakresie:</span><span class="sxs-lookup"><span data-stu-id="674c7-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="674c7-218">Najlepszym rozwiązaniem jest uniknięcie kompilowania dat twardych w modelach, dlatego przy użyciu `[Range]` atrybutu i `DateTime` nie jest to odradzane.</span><span class="sxs-lookup"><span data-stu-id="674c7-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="674c7-219">Użyj [konfiguracji](xref:fundamentals/configuration/index) dla zakresów dat i innych wartości, które podlegają częstym zmianom, zamiast określania ich w kodzie.</span><span class="sxs-lookup"><span data-stu-id="674c7-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="674c7-220">Poniższy kod ilustruje łączenie atrybutów w jednym wierszu:</span><span class="sxs-lookup"><span data-stu-id="674c7-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="674c7-221">[Wprowadzenie do :::no-loc(Razor)::: Na stronach i EF Core](xref:data/ef-rp/intro) są wyświetlane zaawansowane operacje EF Core z :::no-loc(Razor)::: stronami.</span><span class="sxs-lookup"><span data-stu-id="674c7-221">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with :::no-loc(Razor)::: Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="674c7-222">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="674c7-222">Apply migrations</span></span>

<span data-ttu-id="674c7-223">Adnotacje zastosowane do klasy zmieniają schemat.</span><span class="sxs-lookup"><span data-stu-id="674c7-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="674c7-224">Na przykład, adnotacje zastosowane do `Title` pola:</span><span class="sxs-lookup"><span data-stu-id="674c7-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="674c7-225">Ogranicza znaki do 60.</span><span class="sxs-lookup"><span data-stu-id="674c7-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="674c7-226">Nie zezwala na `null` wartość.</span><span class="sxs-lookup"><span data-stu-id="674c7-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="674c7-227">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674c7-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="674c7-228">`Movie`Tabela ma obecnie następujący schemat:</span><span class="sxs-lookup"><span data-stu-id="674c7-228">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="674c7-229">Powyższe zmiany schematu nie powodują wygenerowania wyjątku przez EF.</span><span class="sxs-lookup"><span data-stu-id="674c7-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="674c7-230">Należy jednak utworzyć migrację, aby schemat był spójny z modelem.</span><span class="sxs-lookup"><span data-stu-id="674c7-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="674c7-231">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="674c7-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="674c7-232">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="674c7-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="674c7-233">`Update-Database` uruchamia `Up` metody `New_DataAnnotations` klasy.</span><span class="sxs-lookup"><span data-stu-id="674c7-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="674c7-234">Przeanalizuj metodę `Up`:</span><span class="sxs-lookup"><span data-stu-id="674c7-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="674c7-235">Zaktualizowana `Movie` tabela ma następujący schemat:</span><span class="sxs-lookup"><span data-stu-id="674c7-235">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="674c7-236">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="674c7-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="674c7-237">Migracja nie jest wymagana w przypadku oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="674c7-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="674c7-238">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="674c7-238">Publish to Azure</span></span>

<span data-ttu-id="674c7-239">Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji ASP.NET Core na platformie Azure przy użyciu SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="674c7-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="674c7-240">Dziękujemy za zakończenie tego wprowadzenia do :::no-loc(Razor)::: stron.</span><span class="sxs-lookup"><span data-stu-id="674c7-240">Thanks for completing this introduction to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="674c7-241">[Wprowadzenie do :::no-loc(Razor)::: Strony i EF Core](xref:data/ef-rp/intro) są doskonałym zaobserwują się z tym samouczkiem.</span><span class="sxs-lookup"><span data-stu-id="674c7-241">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="674c7-242">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="674c7-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="674c7-243">Poprzedni: Dodaj nowe pole</span><span class="sxs-lookup"><span data-stu-id="674c7-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
