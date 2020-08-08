---
title: Walidacja modelu w ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się więcej o walidacji modelu w ASP.NET Core MVC i Razor stronach.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/validation
ms.openlocfilehash: c0edf56c966cb90c1c308f300a8944d392fdc0e7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020980"
---
# <a name="model-validation-in-aspnet-core-mvc-and-no-locrazor-pages"></a><span data-ttu-id="228b3-103">Walidacja modelu w ASP.NET Core MVC i Razor stronach</span><span class="sxs-lookup"><span data-stu-id="228b3-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="228b3-104">Autor [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="228b3-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="228b3-105">W tym artykule wyjaśniono, jak sprawdzić poprawność danych wprowadzonych przez użytkownika w aplikacji ASP.NET Core MVC lub Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="228b3-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="228b3-106">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="228b3-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="228b3-107">Stan modelu</span><span class="sxs-lookup"><span data-stu-id="228b3-107">Model state</span></span>

<span data-ttu-id="228b3-108">Stan modelu reprezentuje błędy pochodzące z dwóch podsystemów: powiązanie modelu i walidacja modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="228b3-109">Błędy, które pochodzą z [powiązania modelu](model-binding.md) , są zwykle Błędy konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="228b3-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="228b3-110">Na przykład znak "x" jest wprowadzany w polu liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="228b3-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="228b3-111">Walidacja modelu odbywa się po powiązaniu modelu i zgłasza błędy, gdy dane nie są zgodne z regułami biznesowymi.</span><span class="sxs-lookup"><span data-stu-id="228b3-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="228b3-112">Na przykład wartość 0 jest wprowadzana w polu, które oczekuje klasyfikacji z przedziału od 1 do 5.</span><span class="sxs-lookup"><span data-stu-id="228b3-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="228b3-113">Powiązanie modelu i walidacja modelu są wykonywane przed wykonaniem akcji kontrolera lub Razor metody obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="228b3-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="228b3-114">W przypadku aplikacji sieci Web jest odpowiedzialna za jej sprawdzenie `ModelState.IsValid` i reagowanie.</span><span class="sxs-lookup"><span data-stu-id="228b3-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="228b3-115">Aplikacja internetowa zazwyczaj ponownie wyświetla stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="228b3-116">Kontrolery interfejsu API sieci Web nie muszą sprawdzać `ModelState.IsValid` , czy mają `[ApiController]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="228b3-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="228b3-117">W takim przypadku automatyczna odpowiedź HTTP 400 zawierająca szczegóły błędu jest zwracana, gdy stan modelu jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="228b3-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="228b3-118">Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="228b3-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="228b3-119">Uruchom ponownie weryfikację</span><span class="sxs-lookup"><span data-stu-id="228b3-119">Rerun validation</span></span>

<span data-ttu-id="228b3-120">Walidacja jest automatyczna, ale warto powtórzyć ją ręcznie.</span><span class="sxs-lookup"><span data-stu-id="228b3-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="228b3-121">Na przykład można obliczyć wartość właściwości i chcieć ponownie uruchomić weryfikację po ustawieniu właściwości na wartość obliczaną.</span><span class="sxs-lookup"><span data-stu-id="228b3-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="228b3-122">Aby ponownie uruchomić weryfikację, wywołaj `TryValidateModel` metodę, jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="228b3-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="228b3-123">Atrybuty walidacji</span><span class="sxs-lookup"><span data-stu-id="228b3-123">Validation attributes</span></span>

<span data-ttu-id="228b3-124">Atrybuty walidacji umożliwiają określanie reguł walidacji dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="228b3-125">W poniższym przykładzie z przykładowej aplikacji przedstawiono klasę modelu, która ma adnotację z atrybutami walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="228b3-126">Ten `[ClassicMovie]` atrybut jest niestandardowym atrybutem walidacji, a inne są wbudowane.</span><span class="sxs-lookup"><span data-stu-id="228b3-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="228b3-127">Niepokazywany `[ClassicMovieWithClientValidator]` .</span><span class="sxs-lookup"><span data-stu-id="228b3-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="228b3-128">`[ClassicMovieWithClientValidator]`pokazuje alternatywny sposób implementacji atrybutu niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="228b3-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="228b3-129">Atrybuty wbudowane</span><span class="sxs-lookup"><span data-stu-id="228b3-129">Built-in attributes</span></span>

<span data-ttu-id="228b3-130">Poniżej przedstawiono niektóre wbudowane atrybuty walidacji:</span><span class="sxs-lookup"><span data-stu-id="228b3-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="228b3-131">`[CreditCard]`: Sprawdza, czy właściwość ma format karty kredytowej.</span><span class="sxs-lookup"><span data-stu-id="228b3-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span> <span data-ttu-id="228b3-132">Wymaga zastosowania [dodatkowych metod walidacji jQuery](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span><span class="sxs-lookup"><span data-stu-id="228b3-132">Requires [jQuery Validation Additional Methods](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span></span>
* <span data-ttu-id="228b3-133">`[Compare]`: Sprawdza, czy dwie właściwości w modelu pasują do siebie.</span><span class="sxs-lookup"><span data-stu-id="228b3-133">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="228b3-134">`[EmailAddress]`: Sprawdza, czy właściwość ma format wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="228b3-134">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="228b3-135">`[Phone]`: Sprawdza, czy właściwość ma format numeru telefonu.</span><span class="sxs-lookup"><span data-stu-id="228b3-135">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="228b3-136">`[Range]`: Sprawdza, czy wartość właściwości znajduje się w określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="228b3-136">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="228b3-137">`[RegularExpression]`: Sprawdza, czy wartość właściwości jest zgodna z określonym wyrażeniem regularnym.</span><span class="sxs-lookup"><span data-stu-id="228b3-137">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="228b3-138">`[Required]`: Sprawdza, czy pole nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="228b3-138">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="228b3-139">Zobacz [ `[Required]` atrybut](#required-attribute) , aby uzyskać szczegółowe informacje o zachowaniu tego atrybutu.</span><span class="sxs-lookup"><span data-stu-id="228b3-139">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="228b3-140">`[StringLength]`: Sprawdza, czy wartość właściwości String nie przekracza podanego limitu długości.</span><span class="sxs-lookup"><span data-stu-id="228b3-140">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="228b3-141">`[Url]`: Sprawdza, czy właściwość ma format adresu URL.</span><span class="sxs-lookup"><span data-stu-id="228b3-141">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="228b3-142">`[Remote]`: Sprawdza poprawność danych wejściowych na kliencie przez wywołanie metody akcji na serwerze.</span><span class="sxs-lookup"><span data-stu-id="228b3-142">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="228b3-143">Zobacz [ `[Remote]` atrybut](#remote-attribute) , aby uzyskać szczegółowe informacje o zachowaniu tego atrybutu.</span><span class="sxs-lookup"><span data-stu-id="228b3-143">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="228b3-144">Pełną listę atrybutów sprawdzania poprawności można znaleźć w przestrzeni nazw [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="228b3-144">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="228b3-145">Komunikaty o błędach</span><span class="sxs-lookup"><span data-stu-id="228b3-145">Error messages</span></span>

<span data-ttu-id="228b3-146">Atrybuty walidacji pozwalają określić komunikat o błędzie, który ma być wyświetlany dla nieprawidłowych danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="228b3-146">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="228b3-147">Przykład:</span><span class="sxs-lookup"><span data-stu-id="228b3-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="228b3-148">Wewnętrznie atrybuty są wywoływane `String.Format` przy użyciu symbolu zastępczego dla nazwy pola i czasami dodatkowych symboli zastępczych.</span><span class="sxs-lookup"><span data-stu-id="228b3-148">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="228b3-149">Przykład:</span><span class="sxs-lookup"><span data-stu-id="228b3-149">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="228b3-150">W przypadku zastosowania do `Name` Właściwości komunikat o błędzie utworzony przez poprzedni kod powinien mieć wartość "nazwa musi zawierać się w przedziale od 6 do 8".</span><span class="sxs-lookup"><span data-stu-id="228b3-150">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="228b3-151">Aby dowiedzieć się, które parametry są przesyłane do `String.Format` komunikatu o błędzie określonego atrybutu, zobacz [kod źródłowy adnotacji](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)danych.</span><span class="sxs-lookup"><span data-stu-id="228b3-151">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="228b3-152">[Required] — atrybut</span><span class="sxs-lookup"><span data-stu-id="228b3-152">[Required] attribute</span></span>

<span data-ttu-id="228b3-153">System sprawdzania poprawności w programie .NET Core 3,0 lub nowszy traktuje parametry niedopuszczające wartości null lub właściwości powiązane tak, jakby miały `[Required]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="228b3-153">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="228b3-154">[Typy wartości](/dotnet/csharp/language-reference/keywords/value-types) , takie jak `decimal` i, `int` nie dopuszczają wartości null.</span><span class="sxs-lookup"><span data-stu-id="228b3-154">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="228b3-155">To zachowanie można wyłączyć przez skonfigurowanie <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="228b3-155">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="228b3-156">[Wymagane] Walidacja na serwerze</span><span class="sxs-lookup"><span data-stu-id="228b3-156">[Required] validation on the server</span></span>

<span data-ttu-id="228b3-157">Na serwerze, wymagana wartość jest uważana za brakującą, jeśli właściwość ma wartość null.</span><span class="sxs-lookup"><span data-stu-id="228b3-157">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="228b3-158">Pole niedopuszczające wartości null jest zawsze prawidłowe i `[Required]` komunikat o błędzie nie jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="228b3-158">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="228b3-159">Jednak powiązanie modelu dla właściwości niedopuszczających wartości null może zakończyć się niepowodzeniem, co spowoduje wystąpienie komunikatu o błędzie, takiego jak `The value '' is invalid` .</span><span class="sxs-lookup"><span data-stu-id="228b3-159">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="228b3-160">Aby określić niestandardowy komunikat o błędzie dla weryfikacji po stronie serwera dla typów niedopuszczających wartości null, dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="228b3-160">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="228b3-161">Wprowadź wartość pola Nullable (np `decimal?` `decimal` . zamiast).</span><span class="sxs-lookup"><span data-stu-id="228b3-161">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="228b3-162">[Dopuszcza \<T> wartość null](/dotnet/csharp/programming-guide/nullable-types/) typy wartości są traktowane jak standardowe typy dopuszczające wartości null.</span><span class="sxs-lookup"><span data-stu-id="228b3-162">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="228b3-163">Określ domyślny komunikat o błędzie, który ma być używany przez powiązanie modelu, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-163">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="228b3-164">Aby uzyskać więcej informacji o błędach powiązania modelu, dla których można ustawić domyślne komunikaty dla programu, zobacz <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> .</span><span class="sxs-lookup"><span data-stu-id="228b3-164">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="228b3-165">[Wymagane] weryfikacja na kliencie</span><span class="sxs-lookup"><span data-stu-id="228b3-165">[Required] validation on the client</span></span>

<span data-ttu-id="228b3-166">Typy niedopuszczające wartości null i ciągi są obsługiwane inaczej na kliencie w porównaniu z serwerem.</span><span class="sxs-lookup"><span data-stu-id="228b3-166">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="228b3-167">Na kliencie:</span><span class="sxs-lookup"><span data-stu-id="228b3-167">On the client:</span></span>

* <span data-ttu-id="228b3-168">Wartość jest uważana za obecną tylko wtedy, gdy wprowadzono dla niej dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-168">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="228b3-169">W związku z tym, walidacja po stronie klienta obsługuje niedopuszczające wartości null typy takie same jak typy dopuszczające wartości null.</span><span class="sxs-lookup"><span data-stu-id="228b3-169">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="228b3-170">Biały znak w polu ciągu jest uznawany za prawidłowe dane wejściowe przez [wymaganą](https://jqueryvalidation.org/required-method/) metodę weryfikacji jQuery.</span><span class="sxs-lookup"><span data-stu-id="228b3-170">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="228b3-171">Walidacja po stronie serwera traktuje wymagane pole ciągu nieprawidłowe, jeśli wprowadzono tylko odstępy.</span><span class="sxs-lookup"><span data-stu-id="228b3-171">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="228b3-172">Jak wspomniano wcześniej, typy niedopuszczające wartości null są traktowane tak, jakby miały `[Required]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="228b3-172">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="228b3-173">Oznacza to, że można uzyskać weryfikację po stronie klienta, nawet jeśli nie zastosowano `[Required]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="228b3-173">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="228b3-174">Ale jeśli nie używasz tego atrybutu, zostanie wyświetlony domyślny komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-174">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="228b3-175">Aby określić niestandardowy komunikat o błędzie, Użyj atrybutu.</span><span class="sxs-lookup"><span data-stu-id="228b3-175">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="228b3-176">[Remote] — atrybut</span><span class="sxs-lookup"><span data-stu-id="228b3-176">[Remote] attribute</span></span>

<span data-ttu-id="228b3-177">Ten `[Remote]` atrybut implementuje walidację po stronie klienta, która wymaga wywołania metody na serwerze w celu określenia, czy dane wejściowe pola są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-177">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="228b3-178">Na przykład aplikacja może wymagać sprawdzenia, czy nazwa użytkownika jest już używana.</span><span class="sxs-lookup"><span data-stu-id="228b3-178">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="228b3-179">Aby zaimplementować zdalne sprawdzanie poprawności:</span><span class="sxs-lookup"><span data-stu-id="228b3-179">To implement remote validation:</span></span>

1. <span data-ttu-id="228b3-180">Utwórz metodę akcji dla języka JavaScript do wywołania.</span><span class="sxs-lookup"><span data-stu-id="228b3-180">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="228b3-181">Metoda [zdalna](https://jqueryvalidation.org/remote-method/) walidacji jQuery oczekuje odpowiedzi JSON:</span><span class="sxs-lookup"><span data-stu-id="228b3-181">The jQuery Validation [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="228b3-182">`true`oznacza, że dane wejściowe są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-182">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="228b3-183">`false`, `undefined` lub `null` oznacza, że dane wejściowe są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-183">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="228b3-184">Wyświetl domyślny komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-184">Display the default error message.</span></span>
   * <span data-ttu-id="228b3-185">Każdy inny ciąg oznacza, że dane wejściowe są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-185">Any other string means the input is invalid.</span></span> <span data-ttu-id="228b3-186">Wyświetl ciąg jako niestandardowy komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-186">Display the string as a custom error message.</span></span>

   <span data-ttu-id="228b3-187">Oto przykład metody akcji, która zwraca niestandardowy komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-187">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="228b3-188">W klasie modelu Dodaj adnotację do właściwości z atrybutem wskazującym na `[Remote]` metodę akcji walidacji, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-188">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="228b3-189">Ten `[Remote]` atrybut znajduje się w `Microsoft.AspNetCore.Mvc` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="228b3-189">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="228b3-190">Dodatkowe pola</span><span class="sxs-lookup"><span data-stu-id="228b3-190">Additional fields</span></span>

<span data-ttu-id="228b3-191">`AdditionalFields`Właściwość `[Remote]` atrybutu umożliwia Weryfikowanie kombinacji pól względem danych na serwerze.</span><span class="sxs-lookup"><span data-stu-id="228b3-191">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="228b3-192">Na przykład jeśli `User` model ma `FirstName` i `LastName` właściwości, można sprawdzić, czy żaden istniejący użytkownik już ma tę parę nazw.</span><span class="sxs-lookup"><span data-stu-id="228b3-192">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="228b3-193">Poniższy przykład przedstawia sposób użycia `AdditionalFields` :</span><span class="sxs-lookup"><span data-stu-id="228b3-193">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="228b3-194">`AdditionalFields`można jawnie ustawić dla ciągów "FirstName" i "LastName", ale użycie operatora [nameof](/dotnet/csharp/language-reference/keywords/nameof) upraszcza późniejsze refaktoryzacje.</span><span class="sxs-lookup"><span data-stu-id="228b3-194">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="228b3-195">Metoda akcji dla tej walidacji musi akceptować zarówno `firstName` argumenty, jak i `lastName` :</span><span class="sxs-lookup"><span data-stu-id="228b3-195">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="228b3-196">Gdy użytkownik wprowadzi imię lub nazwisko, kod JavaScript wykonuje zdalne wywołanie, aby sprawdzić, czy ta para nazw została podjęta.</span><span class="sxs-lookup"><span data-stu-id="228b3-196">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="228b3-197">Aby sprawdzić poprawność dwóch lub więcej pól, podaj je jako listę rozdzielaną przecinkami.</span><span class="sxs-lookup"><span data-stu-id="228b3-197">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="228b3-198">Na przykład aby dodać `MiddleName` Właściwość do modelu, należy ustawić `[Remote]` atrybut, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-198">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="228b3-199">`AdditionalFields`, podobnie jak wszystkie argumenty atrybutu, musi być wyrażeniem stałym.</span><span class="sxs-lookup"><span data-stu-id="228b3-199">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="228b3-200">W związku z tym nie należy używać [interpolowanego ciągu](/dotnet/csharp/language-reference/keywords/interpolated-strings) ani wywołania <xref:System.String.Join*> do inicjowania `AdditionalFields` .</span><span class="sxs-lookup"><span data-stu-id="228b3-200">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="228b3-201">Alternatywy dla wbudowanych atrybutów</span><span class="sxs-lookup"><span data-stu-id="228b3-201">Alternatives to built-in attributes</span></span>

<span data-ttu-id="228b3-202">Jeśli potrzebujesz weryfikacji niedostarczonej przez wbudowane atrybuty, możesz:</span><span class="sxs-lookup"><span data-stu-id="228b3-202">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="228b3-203">[Tworzenie atrybutów niestandardowych](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="228b3-203">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="228b3-204">[Zaimplementuj IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="228b3-204">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="228b3-205">Atrybuty niestandardowe</span><span class="sxs-lookup"><span data-stu-id="228b3-205">Custom attributes</span></span>

<span data-ttu-id="228b3-206">W przypadku scenariuszy, w których wbudowane atrybuty walidacji nie obsługują, można utworzyć niestandardowe atrybuty walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-206">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="228b3-207">Utwórz klasę, która dziedziczy z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> , i Zastąp <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodę.</span><span class="sxs-lookup"><span data-stu-id="228b3-207">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="228b3-208">`IsValid`Metoda akceptuje obiekt o nazwie *Value*, czyli dane wejściowe do zweryfikowania.</span><span class="sxs-lookup"><span data-stu-id="228b3-208">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="228b3-209">Przeciążenie akceptuje również `ValidationContext` obiekt, który zawiera dodatkowe informacje, takie jak wystąpienie modelu utworzone przez powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-209">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="228b3-210">Poniższy przykład sprawdza, czy Data wydania filmu w *klasycznym* gatunku nie jest późniejsza niż określony rok.</span><span class="sxs-lookup"><span data-stu-id="228b3-210">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="228b3-211">`[ClassicMovie]`Atrybut:</span><span class="sxs-lookup"><span data-stu-id="228b3-211">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="228b3-212">Jest uruchamiany tylko na serwerze.</span><span class="sxs-lookup"><span data-stu-id="228b3-212">Is only run on the server.</span></span>
* <span data-ttu-id="228b3-213">W przypadku klasycznych filmów program sprawdza poprawność daty wydania:</span><span class="sxs-lookup"><span data-stu-id="228b3-213">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="228b3-214">`movie`Zmienna w poprzednim przykładzie reprezentuje `Movie` obiekt, który zawiera dane z przesłania formularza.</span><span class="sxs-lookup"><span data-stu-id="228b3-214">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="228b3-215">Gdy Walidacja nie powiedzie się, `ValidationResult` zostanie zwrócony komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-215">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="228b3-216">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="228b3-216">IValidatableObject</span></span>

<span data-ttu-id="228b3-217">Poprzedni przykład działa tylko z `Movie` typami.</span><span class="sxs-lookup"><span data-stu-id="228b3-217">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="228b3-218">Kolejną opcją weryfikacji na poziomie klasy jest implementacja `IValidatableObject` w klasie modelu, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-218">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="228b3-219">Sprawdzanie poprawności węzła najwyższego poziomu</span><span class="sxs-lookup"><span data-stu-id="228b3-219">Top-level node validation</span></span>

<span data-ttu-id="228b3-220">Węzły najwyższego poziomu obejmują:</span><span class="sxs-lookup"><span data-stu-id="228b3-220">Top-level nodes include:</span></span>

* <span data-ttu-id="228b3-221">Parametry akcji</span><span class="sxs-lookup"><span data-stu-id="228b3-221">Action parameters</span></span>
* <span data-ttu-id="228b3-222">Właściwości kontrolera</span><span class="sxs-lookup"><span data-stu-id="228b3-222">Controller properties</span></span>
* <span data-ttu-id="228b3-223">Parametry procedury obsługi stron</span><span class="sxs-lookup"><span data-stu-id="228b3-223">Page handler parameters</span></span>
* <span data-ttu-id="228b3-224">Właściwości modelu strony</span><span class="sxs-lookup"><span data-stu-id="228b3-224">Page model properties</span></span>

<span data-ttu-id="228b3-225">Węzły najwyższego poziomu powiązane z modelem są weryfikowane jako uzupełnienie właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-225">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="228b3-226">W poniższym przykładzie z przykładowej aplikacji `VerifyPhone` Metoda używa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> do walidacji `phone` parametru Action:</span><span class="sxs-lookup"><span data-stu-id="228b3-226">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="228b3-227">Węzły najwyższego poziomu mogą korzystać <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> z atrybutów walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-227">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="228b3-228">W poniższym przykładzie z przykładowej aplikacji `CheckAge` Metoda określa, że `age` parametr musi być powiązany z ciągu zapytania, gdy formularz zostanie przesłany:</span><span class="sxs-lookup"><span data-stu-id="228b3-228">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="228b3-229">Na stronie sprawdzanie wieku (*Sprawdź. cshtml*) Istnieją dwa formy.</span><span class="sxs-lookup"><span data-stu-id="228b3-229">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="228b3-230">Pierwszy formularz przesyła `Age` wartość `99` jako parametr ciągu zapytania: `https://localhost:5001/Users/CheckAge?Age=99` .</span><span class="sxs-lookup"><span data-stu-id="228b3-230">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="228b3-231">Po przesłaniu poprawnie sformatowanego `age` parametru z ciągu zapytania, formularz sprawdza poprawność.</span><span class="sxs-lookup"><span data-stu-id="228b3-231">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="228b3-232">Drugi formularz na stronie sprawdzanie wieku przesyła `Age` wartość w treści żądania, a Walidacja nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="228b3-232">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="228b3-233">Powiązanie nie powiodło się, ponieważ `age` parametr musi pochodzić z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="228b3-233">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="228b3-234">Maksymalna liczba błędów</span><span class="sxs-lookup"><span data-stu-id="228b3-234">Maximum errors</span></span>

<span data-ttu-id="228b3-235">Walidacja jest zatrzymywana, gdy zostanie osiągnięta maksymalna liczba błędów (domyślnie 200).</span><span class="sxs-lookup"><span data-stu-id="228b3-235">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="228b3-236">Tę liczbę można skonfigurować przy użyciu następującego kodu w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="228b3-236">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="228b3-237">Maksymalna rekursja</span><span class="sxs-lookup"><span data-stu-id="228b3-237">Maximum recursion</span></span>

<span data-ttu-id="228b3-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>przechodzi do grafu obiektów sprawdzanego przez model.</span><span class="sxs-lookup"><span data-stu-id="228b3-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="228b3-239">W przypadku modeli, które są głębokie lub nieskończonie cykliczne, walidacja może spowodować przepełnienie stosu.</span><span class="sxs-lookup"><span data-stu-id="228b3-239">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="228b3-240">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) umożliwia szybkie zakończenie sprawdzania poprawności, Jeśli rekursja odwiedzających przekracza skonfigurowaną głębokość.</span><span class="sxs-lookup"><span data-stu-id="228b3-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="228b3-241">Wartość domyślna `MvcOptions.MaxValidationDepth` to 32.</span><span class="sxs-lookup"><span data-stu-id="228b3-241">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="228b3-242">Automatyczny krótki obwód</span><span class="sxs-lookup"><span data-stu-id="228b3-242">Automatic short-circuit</span></span>

<span data-ttu-id="228b3-243">Sprawdzanie poprawności jest automatycznie skracane (pomijane), jeśli wykres modelu nie wymaga weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="228b3-244">Obiekty, dla których środowisko uruchomieniowe pomija sprawdzanie poprawności dla dołączania kolekcji elementów podstawowych (takich jak `byte[]` , `string[]` , `Dictionary<string, string>` ) i złożonych wykresów obiektów, które nie mają żadnych modułów sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="228b3-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="228b3-245">Wyłącz weryfikację</span><span class="sxs-lookup"><span data-stu-id="228b3-245">Disable validation</span></span>

<span data-ttu-id="228b3-246">Aby wyłączyć weryfikację:</span><span class="sxs-lookup"><span data-stu-id="228b3-246">To disable validation:</span></span>

1. <span data-ttu-id="228b3-247">Utwórz implementację `IObjectModelValidator` , która nie oznacza żadnych pól jako nieprawidłowych.</span><span class="sxs-lookup"><span data-stu-id="228b3-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="228b3-248">Dodaj następujący kod, aby `Startup.ConfigureServices` zastąpić domyślną `IObjectModelValidator` implementację w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="228b3-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="228b3-249">Nadal mogą pojawić się błędy stanu modelu pochodzące z powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="228b3-250">Weryfikacja po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-250">Client-side validation</span></span>

<span data-ttu-id="228b3-251">Weryfikacja po stronie klienta uniemożliwia przesyłanie, dopóki formularz nie będzie prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="228b3-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="228b3-252">Przycisk Prześlij uruchamia kod JavaScript, który przesyła formularz lub wyświetla komunikaty o błędach.</span><span class="sxs-lookup"><span data-stu-id="228b3-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="228b3-253">Weryfikacja po stronie klienta umożliwia uniknięcie niepotrzebnej komunikacji z serwerem w przypadku błędów danych wejściowych w formularzu.</span><span class="sxs-lookup"><span data-stu-id="228b3-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="228b3-254">Następujący skrypt odwołuje się do *_Layout. cshtml* i *_ValidationScriptsPartial. cshtml* obsługuje walidację po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="228b3-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="228b3-255">Skrypt [niezauważalnego sprawdzania poprawności jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) jest niestandardową biblioteką frontonu firmy Microsoft, która kompiluje się w popularnej wtyczki do [sprawdzania poprawności jQuery](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="228b3-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validation](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="228b3-256">Bez dyskretnej weryfikacji jQuery należy wykonać kod tej samej logiki walidacji w dwóch miejscach: raz w atrybuty walidacji po stronie serwera we właściwościach modelu, a następnie ponownie w skryptach po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="228b3-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="228b3-257">Zamiast tego, [pomocników tagów](xref:mvc/views/tag-helpers/intro) i [pomocników HTML](xref:mvc/views/overview) używają atrybutów walidacji i metadanych typu z właściwości modelu do renderowania atrybutów HTML 5 `data-` dla elementów formularza, które wymagają walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="228b3-258">niezauważalne sprawdzenie poprawności przez program jQuery umożliwia przeanalizowanie `data-` atrybutów i przekazanie logiki do walidacji jQuery, efektywne "Kopiowanie" logiki walidacji po stronie serwera do klienta.</span><span class="sxs-lookup"><span data-stu-id="228b3-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validation, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="228b3-259">Błędy sprawdzania poprawności można wyświetlić na kliencie przy użyciu pomocników tagów, jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="228b3-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="228b3-260">Poprzednie pomocnicy tagów renderują następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="228b3-260">The preceding tag helpers render the following HTML:</span></span>

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

<span data-ttu-id="228b3-261">Zauważ, że `data-` atrybuty w danych wyjściowych HTML odpowiadają atrybutom walidacji `Movie.ReleaseDate` właściwości.</span><span class="sxs-lookup"><span data-stu-id="228b3-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="228b3-262">Ten `data-val-required` atrybut zawiera komunikat o błędzie, który zostanie wyświetlony, jeśli użytkownik nie wypełni pola Data wydania.</span><span class="sxs-lookup"><span data-stu-id="228b3-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="228b3-263">niezauważalne sprawdzenie poprawności przez funkcję jQuery spowoduje przekazanie tej wartości do metody sprawdzania poprawności jQuery [()](https://jqueryvalidation.org/required-method/) , która następnie wyświetla ten komunikat w elemencie towarzyszącym **\<span>** .</span><span class="sxs-lookup"><span data-stu-id="228b3-263">jQuery Unobtrusive Validation passes this value to the jQuery Validation [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="228b3-264">Walidacja typu danych jest oparta na typie .NET właściwości, chyba że zostanie zastąpiona przez `[DataType]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="228b3-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="228b3-265">Przeglądarki mają własne domyślne komunikaty o błędach, ale pakietem weryfikacji jQuery nie dyskretnego sprawdzania poprawności może przesłonić te komunikaty.</span><span class="sxs-lookup"><span data-stu-id="228b3-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="228b3-266">`[DataType]`atrybuty i podklasy, takie jak `[EmailAddress]` pozwalają określić komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="228b3-267">Niezauważalna weryfikacja</span><span class="sxs-lookup"><span data-stu-id="228b3-267">Unobtrusive validation</span></span>

<span data-ttu-id="228b3-268">Aby uzyskać informacje o niezauważalnej weryfikacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/1111)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="228b3-268">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="228b3-269">Dodawanie walidacji do formularzy dynamicznych</span><span class="sxs-lookup"><span data-stu-id="228b3-269">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="228b3-270">niezauważalne Walidacja w usłudze jQuery powoduje przekazanie logiki walidacji i parametrów do walidacji jQuery podczas pierwszego ładowania strony.</span><span class="sxs-lookup"><span data-stu-id="228b3-270">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validation when the page first loads.</span></span> <span data-ttu-id="228b3-271">W związku z tym sprawdzanie poprawności nie działa automatycznie na formularzach generowanych dynamicznie.</span><span class="sxs-lookup"><span data-stu-id="228b3-271">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="228b3-272">Aby włączyć weryfikację, poinformuj jQuery o niezauważalnej weryfikacji, aby przeanalizować formularz dynamiczny bezpośrednio po jego utworzeniu.</span><span class="sxs-lookup"><span data-stu-id="228b3-272">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="228b3-273">Na przykład poniższy kod konfiguruje walidację po stronie klienta w formularzu dodanym przez AJAX.</span><span class="sxs-lookup"><span data-stu-id="228b3-273">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="228b3-274">`$.validator.unobtrusive.parse()`Metoda akceptuje selektor jQuery dla jednego argumentu.</span><span class="sxs-lookup"><span data-stu-id="228b3-274">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="228b3-275">Ta metoda informuje niedyskretną weryfikację jQuery do analizy `data-` atrybutów formularzy w ramach tego selektora.</span><span class="sxs-lookup"><span data-stu-id="228b3-275">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="228b3-276">Wartości tych atrybutów są następnie przesyłane do wtyczki walidacji jQuery.</span><span class="sxs-lookup"><span data-stu-id="228b3-276">The values of those attributes are then passed to the jQuery Validation plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="228b3-277">Dodawanie walidacji do formantów dynamicznych</span><span class="sxs-lookup"><span data-stu-id="228b3-277">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="228b3-278">`$.validator.unobtrusive.parse()`Metoda działa na całym formularzu, a nie na poszczególnych dynamicznie generowanych kontrolkach, takich jak `<input>` i `<select/>` .</span><span class="sxs-lookup"><span data-stu-id="228b3-278">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="228b3-279">Aby przeanalizować formularz, Usuń dane sprawdzania poprawności, które zostały dodane, gdy formularz został wcześniej przeanalizowany, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-279">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="228b3-280">Niestandardowe sprawdzanie poprawności po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-280">Custom client-side validation</span></span>

<span data-ttu-id="228b3-281">Niestandardowe sprawdzanie poprawności po stronie klienta jest wykonywane przez generowanie `data-` atrybutów HTML, które działają z niestandardowym adapterem weryfikacji platformy jQuery.</span><span class="sxs-lookup"><span data-stu-id="228b3-281">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validation adapter.</span></span> <span data-ttu-id="228b3-282">Następujący przykładowy kod karty został zapisany dla `[ClassicMovie]` `[ClassicMovieWithClientValidator]` atrybutów i, które zostały wprowadzone wcześniej w tym artykule:</span><span class="sxs-lookup"><span data-stu-id="228b3-282">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="228b3-283">Aby uzyskać informacje o sposobach pisania kart sieciowych, zobacz [dokumentację dotyczącą weryfikacji jQuery](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="228b3-283">For information about how to write adapters, see the [jQuery Validation documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="228b3-284">Użycie karty dla danego pola jest wyzwalane przez `data-` atrybuty, które:</span><span class="sxs-lookup"><span data-stu-id="228b3-284">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="228b3-285">Oznacz pole jako podlegające walidacji ( `data-val="true"` ).</span><span class="sxs-lookup"><span data-stu-id="228b3-285">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="228b3-286">Zidentyfikuj nazwę reguły walidacji i tekst komunikatu o błędzie (na przykład `data-val-rulename="Error message."` ).</span><span class="sxs-lookup"><span data-stu-id="228b3-286">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="228b3-287">Podaj wszelkie dodatkowe parametry wymagane przez moduł sprawdzania poprawności (na przykład `data-val-rulename-param1="value"` ).</span><span class="sxs-lookup"><span data-stu-id="228b3-287">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="228b3-288">W poniższym przykładzie przedstawiono `data-` atrybuty dla atrybutu przykładowej aplikacji `ClassicMovie` :</span><span class="sxs-lookup"><span data-stu-id="228b3-288">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="228b3-289">Jak wspomniano wcześniej, [pomocników tagów](xref:mvc/views/tag-helpers/intro) i [pomocników HTML](xref:mvc/views/overview) wykorzystują informacje z atrybutów walidacji do renderowania `data-` atrybutów.</span><span class="sxs-lookup"><span data-stu-id="228b3-289">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="228b3-290">Istnieją dwie opcje pisania kodu, które powoduje tworzenie niestandardowych `data-` atrybutów HTML:</span><span class="sxs-lookup"><span data-stu-id="228b3-290">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="228b3-291">Utwórz klasę, która pochodzi od `AttributeAdapterBase<TAttribute>` i klasy implementującej `IValidationAttributeAdapterProvider` , i Zarejestruj swój atrybut oraz jego kartę w programie di.</span><span class="sxs-lookup"><span data-stu-id="228b3-291">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="228b3-292">Ta metoda jest zgodna z [pojedynczym podmiotem odpowiedzialnym](https://wikipedia.org/wiki/Single_responsibility_principle) w odniesieniu do kodu weryfikacyjnego związanego z serwerem i klienta jest w osobnych klasach.</span><span class="sxs-lookup"><span data-stu-id="228b3-292">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="228b3-293">Adapter ma również zalety, że ponieważ jest on zarejestrowany w programie DI, w razie potrzeby są dostępne inne usługi w programie DI.</span><span class="sxs-lookup"><span data-stu-id="228b3-293">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="228b3-294">Zaimplementuj `IClientModelValidator` w `ValidationAttribute` klasie.</span><span class="sxs-lookup"><span data-stu-id="228b3-294">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="228b3-295">Ta metoda może być odpowiednia, jeśli atrybut nie wykonuje walidacji po stronie serwera i nie wymaga żadnych usług z programu DI.</span><span class="sxs-lookup"><span data-stu-id="228b3-295">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="228b3-296">AttributeAdapter dla weryfikacji po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-296">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="228b3-297">Ta metoda renderowania `data-` atrybutów w kodzie HTML jest używana przez `ClassicMovie` atrybut w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-297">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="228b3-298">Aby dodać weryfikację klienta przy użyciu tej metody:</span><span class="sxs-lookup"><span data-stu-id="228b3-298">To add client validation by using this method:</span></span>

1. <span data-ttu-id="228b3-299">Utwórz klasę adaptera atrybutów dla niestandardowego atrybutu walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-299">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="228b3-300">Utwórz klasę z [AttributeAdapterBase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="228b3-300">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="228b3-301">Utwórz `AddValidation` metodę, która dodaje `data-` atrybuty do renderowanych danych wyjściowych, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-301">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="228b3-302">Utwórz klasę dostawcy kart implementującą <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> .</span><span class="sxs-lookup"><span data-stu-id="228b3-302">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="228b3-303">W `GetAttributeAdapter` metodzie Przekaż atrybut niestandardowy do konstruktora adaptera, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-303">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="228b3-304">Zarejestruj dostawcę karty dla DI in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="228b3-304">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="228b3-305">IClientModelValidator dla weryfikacji po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-305">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="228b3-306">Ta metoda renderowania `data-` atrybutów w kodzie HTML jest używana przez `ClassicMovieWithClientValidator` atrybut w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-306">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="228b3-307">Aby dodać weryfikację klienta przy użyciu tej metody:</span><span class="sxs-lookup"><span data-stu-id="228b3-307">To add client validation by using this method:</span></span>

* <span data-ttu-id="228b3-308">W atrybucie niestandardowego sprawdzania poprawności Zaimplementuj `IClientModelValidator` interfejs i Utwórz `AddValidation` metodę.</span><span class="sxs-lookup"><span data-stu-id="228b3-308">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="228b3-309">W `AddValidation` metodzie Dodaj `data-` atrybuty do walidacji, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-309">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="228b3-310">Wyłącz weryfikację po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-310">Disable client-side validation</span></span>

<span data-ttu-id="228b3-311">Poniższy kod wyłącza weryfikację klienta na Razor stronach:</span><span class="sxs-lookup"><span data-stu-id="228b3-311">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="228b3-312">Inne opcje wyłączenia weryfikacji po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="228b3-312">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="228b3-313">Dodaj komentarz do odwołania do `_ValidationScriptsPartial` wszystkich plików *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="228b3-313">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="228b3-314">Usuń zawartość pliku *Pages\Shared \_ ValidationScriptsPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="228b3-314">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="228b3-315">Poprzednie podejście nie zapobiega weryfikacji po stronie klienta ASP.NET Core Identity Razor biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="228b3-315">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="228b3-316">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="228b3-316">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="228b3-317">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="228b3-317">Additional resources</span></span>

* [<span data-ttu-id="228b3-318">Przestrzeń nazw System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="228b3-318">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="228b3-319">Powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="228b3-319">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="228b3-320">W tym artykule wyjaśniono, jak sprawdzić poprawność danych wprowadzonych przez użytkownika w aplikacji ASP.NET Core MVC lub Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="228b3-320">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="228b3-321">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="228b3-321">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="228b3-322">Stan modelu</span><span class="sxs-lookup"><span data-stu-id="228b3-322">Model state</span></span>

<span data-ttu-id="228b3-323">Stan modelu reprezentuje błędy pochodzące z dwóch podsystemów: powiązanie modelu i walidacja modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-323">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="228b3-324">Błędy, które pochodzą z [powiązania modelu](model-binding.md) , to zwykle Błędy konwersji danych (na przykład "x" jest wprowadzany w polu, w którym jest oczekiwana liczba całkowita).</span><span class="sxs-lookup"><span data-stu-id="228b3-324">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="228b3-325">Walidacja modelu odbywa się po powiązaniu modelu i zgłasza błędy, gdy dane nie są zgodne z regułami biznesowymi (na przykład wartość 0 jest wprowadzana w polu, w którym oczekiwana jest Ocena z zakresu od 1 do 5).</span><span class="sxs-lookup"><span data-stu-id="228b3-325">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="228b3-326">Zarówno powiązanie modelu, jak i walidacja są wykonywane przed wykonaniem akcji kontrolera lub Razor metody obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="228b3-326">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="228b3-327">W przypadku aplikacji sieci Web jest odpowiedzialna za jej sprawdzenie `ModelState.IsValid` i reagowanie.</span><span class="sxs-lookup"><span data-stu-id="228b3-327">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="228b3-328">Aplikacja internetowa zazwyczaj ponownie wyświetla stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-328">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="228b3-329">Kontrolery interfejsu API sieci Web nie muszą sprawdzać `ModelState.IsValid` , czy mają `[ApiController]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="228b3-329">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="228b3-330">W takim przypadku automatyczna odpowiedź HTTP 400 zawierająca szczegóły błędu jest zwracana, gdy stan modelu jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="228b3-330">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="228b3-331">Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="228b3-331">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="228b3-332">Uruchom ponownie weryfikację</span><span class="sxs-lookup"><span data-stu-id="228b3-332">Rerun validation</span></span>

<span data-ttu-id="228b3-333">Walidacja jest automatyczna, ale warto powtórzyć ją ręcznie.</span><span class="sxs-lookup"><span data-stu-id="228b3-333">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="228b3-334">Na przykład można obliczyć wartość właściwości i chcieć ponownie uruchomić weryfikację po ustawieniu właściwości na wartość obliczaną.</span><span class="sxs-lookup"><span data-stu-id="228b3-334">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="228b3-335">Aby ponownie uruchomić weryfikację, wywołaj `TryValidateModel` metodę, jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="228b3-335">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="228b3-336">Atrybuty walidacji</span><span class="sxs-lookup"><span data-stu-id="228b3-336">Validation attributes</span></span>

<span data-ttu-id="228b3-337">Atrybuty walidacji umożliwiają określanie reguł walidacji dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-337">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="228b3-338">W poniższym przykładzie z [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) przedstawiono klasę modelu, która ma adnotację z atrybutami walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-338">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="228b3-339">Ten `[ClassicMovie]` atrybut jest niestandardowym atrybutem walidacji, a inne są wbudowane.</span><span class="sxs-lookup"><span data-stu-id="228b3-339">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="228b3-340">Niepokazywany jest `[ClassicMovie2]` , który pokazuje alternatywny sposób implementacji atrybutu niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="228b3-340">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="228b3-341">Atrybuty wbudowane</span><span class="sxs-lookup"><span data-stu-id="228b3-341">Built-in attributes</span></span>

<span data-ttu-id="228b3-342">Wbudowane atrybuty walidacji obejmują:</span><span class="sxs-lookup"><span data-stu-id="228b3-342">Built-in validation attributes include:</span></span>

* <span data-ttu-id="228b3-343">`[CreditCard]`: Sprawdza, czy właściwość ma format karty kredytowej.</span><span class="sxs-lookup"><span data-stu-id="228b3-343">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="228b3-344">`[Compare]`: Sprawdza, czy dwie właściwości w modelu pasują do siebie.</span><span class="sxs-lookup"><span data-stu-id="228b3-344">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="228b3-345">Na przykład plik *register.cshtml.cs* używa `[Compare]` do sprawdzania poprawności dwóch wprowadzonych haseł.</span><span class="sxs-lookup"><span data-stu-id="228b3-345">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="228b3-346">[Szkielet Identity ](xref:security/authentication/scaffold-identity) Aby wyświetlić kod rejestru.</span><span class="sxs-lookup"><span data-stu-id="228b3-346">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="228b3-347">`[EmailAddress]`: Sprawdza, czy właściwość ma format wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="228b3-347">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="228b3-348">`[Phone]`: Sprawdza, czy właściwość ma format numeru telefonu.</span><span class="sxs-lookup"><span data-stu-id="228b3-348">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="228b3-349">`[Range]`: Sprawdza, czy wartość właściwości znajduje się w określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="228b3-349">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="228b3-350">`[RegularExpression]`: Sprawdza, czy wartość właściwości jest zgodna z określonym wyrażeniem regularnym.</span><span class="sxs-lookup"><span data-stu-id="228b3-350">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="228b3-351">`[Required]`: Sprawdza, czy pole nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="228b3-351">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="228b3-352">Zobacz [ `[Required]` atrybut](#required-attribute) , aby uzyskać szczegółowe informacje o zachowaniu tego atrybutu.</span><span class="sxs-lookup"><span data-stu-id="228b3-352">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="228b3-353">`[StringLength]`: Sprawdza, czy wartość właściwości String nie przekracza podanego limitu długości.</span><span class="sxs-lookup"><span data-stu-id="228b3-353">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="228b3-354">`[Url]`: Sprawdza, czy właściwość ma format adresu URL.</span><span class="sxs-lookup"><span data-stu-id="228b3-354">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="228b3-355">`[Remote]`: Sprawdza poprawność danych wejściowych na kliencie przez wywołanie metody akcji na serwerze.</span><span class="sxs-lookup"><span data-stu-id="228b3-355">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="228b3-356">Zobacz [ `[Remote]` atrybut](#remote-attribute) , aby uzyskać szczegółowe informacje o zachowaniu tego atrybutu.</span><span class="sxs-lookup"><span data-stu-id="228b3-356">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="228b3-357">W przypadku używania `[RegularExpression]` atrybutu z walidacją po stronie klienta wyrażenie regularne jest wykonywane w języku JavaScript na kliencie.</span><span class="sxs-lookup"><span data-stu-id="228b3-357">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="228b3-358">Oznacza to, że będzie używane zachowanie zgodne ze standardem [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) .</span><span class="sxs-lookup"><span data-stu-id="228b3-358">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="228b3-359">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/corefx/issues/42487)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="228b3-359">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="228b3-360">Pełną listę atrybutów sprawdzania poprawności można znaleźć w przestrzeni nazw [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="228b3-360">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="228b3-361">Komunikaty o błędach</span><span class="sxs-lookup"><span data-stu-id="228b3-361">Error messages</span></span>

<span data-ttu-id="228b3-362">Atrybuty walidacji pozwalają określić komunikat o błędzie, który ma być wyświetlany dla nieprawidłowych danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="228b3-362">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="228b3-363">Przykład:</span><span class="sxs-lookup"><span data-stu-id="228b3-363">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="228b3-364">Wewnętrznie atrybuty są wywoływane `String.Format` przy użyciu symbolu zastępczego dla nazwy pola i czasami dodatkowych symboli zastępczych.</span><span class="sxs-lookup"><span data-stu-id="228b3-364">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="228b3-365">Przykład:</span><span class="sxs-lookup"><span data-stu-id="228b3-365">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="228b3-366">W przypadku zastosowania do `Name` Właściwości komunikat o błędzie utworzony przez poprzedni kod powinien mieć wartość "nazwa musi zawierać się w przedziale od 6 do 8".</span><span class="sxs-lookup"><span data-stu-id="228b3-366">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="228b3-367">Aby dowiedzieć się, które parametry są przesyłane do `String.Format` komunikatu o błędzie określonego atrybutu, zobacz [kod źródłowy adnotacji](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)danych.</span><span class="sxs-lookup"><span data-stu-id="228b3-367">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="228b3-368">[Required] — atrybut</span><span class="sxs-lookup"><span data-stu-id="228b3-368">[Required] attribute</span></span>

<span data-ttu-id="228b3-369">Domyślnie system walidacji traktuje niedopuszczające wartości null parametry lub właściwości tak, jakby miały `[Required]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="228b3-369">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="228b3-370">[Typy wartości](/dotnet/csharp/language-reference/keywords/value-types) , takie jak `decimal` i, `int` nie dopuszczają wartości null.</span><span class="sxs-lookup"><span data-stu-id="228b3-370">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="228b3-371">[Wymagane] Walidacja na serwerze</span><span class="sxs-lookup"><span data-stu-id="228b3-371">[Required] validation on the server</span></span>

<span data-ttu-id="228b3-372">Na serwerze, wymagana wartość jest uważana za brakującą, jeśli właściwość ma wartość null.</span><span class="sxs-lookup"><span data-stu-id="228b3-372">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="228b3-373">Pole niedopuszczające wartości null jest zawsze prawidłowe, a komunikat o błędzie [Required] nie jest nigdy wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="228b3-373">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="228b3-374">Jednak powiązanie modelu dla właściwości niedopuszczających wartości null może zakończyć się niepowodzeniem, co spowoduje wystąpienie komunikatu o błędzie, takiego jak `The value '' is invalid` .</span><span class="sxs-lookup"><span data-stu-id="228b3-374">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="228b3-375">Aby określić niestandardowy komunikat o błędzie dla weryfikacji po stronie serwera dla typów niedopuszczających wartości null, dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="228b3-375">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="228b3-376">Wprowadź wartość pola Nullable (np `decimal?` `decimal` . zamiast).</span><span class="sxs-lookup"><span data-stu-id="228b3-376">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="228b3-377">[Dopuszcza \<T> wartość null](/dotnet/csharp/programming-guide/nullable-types/) typy wartości są traktowane jak standardowe typy dopuszczające wartości null.</span><span class="sxs-lookup"><span data-stu-id="228b3-377">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="228b3-378">Określ domyślny komunikat o błędzie, który ma być używany przez powiązanie modelu, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-378">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="228b3-379">Aby uzyskać więcej informacji o błędach powiązania modelu, dla których można ustawić domyślne komunikaty dla programu, zobacz <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> .</span><span class="sxs-lookup"><span data-stu-id="228b3-379">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="228b3-380">[Wymagane] weryfikacja na kliencie</span><span class="sxs-lookup"><span data-stu-id="228b3-380">[Required] validation on the client</span></span>

<span data-ttu-id="228b3-381">Typy niedopuszczające wartości null i ciągi są obsługiwane inaczej na kliencie w porównaniu z serwerem.</span><span class="sxs-lookup"><span data-stu-id="228b3-381">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="228b3-382">Na kliencie:</span><span class="sxs-lookup"><span data-stu-id="228b3-382">On the client:</span></span>

* <span data-ttu-id="228b3-383">Wartość jest uważana za obecną tylko wtedy, gdy wprowadzono dla niej dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-383">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="228b3-384">W związku z tym, walidacja po stronie klienta obsługuje niedopuszczające wartości null typy takie same jak typy dopuszczające wartości null.</span><span class="sxs-lookup"><span data-stu-id="228b3-384">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="228b3-385">Biały znak w polu ciągu jest uznawany za prawidłowe dane wejściowe przez [wymaganą](https://jqueryvalidation.org/required-method/) metodę weryfikacji jQuery.</span><span class="sxs-lookup"><span data-stu-id="228b3-385">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="228b3-386">Walidacja po stronie serwera traktuje wymagane pole ciągu nieprawidłowe, jeśli wprowadzono tylko odstępy.</span><span class="sxs-lookup"><span data-stu-id="228b3-386">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="228b3-387">Jak wspomniano wcześniej, typy niedopuszczające wartości null są traktowane tak, jakby miały `[Required]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="228b3-387">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="228b3-388">Oznacza to, że można uzyskać weryfikację po stronie klienta, nawet jeśli nie zastosowano `[Required]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="228b3-388">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="228b3-389">Ale jeśli nie używasz tego atrybutu, zostanie wyświetlony domyślny komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-389">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="228b3-390">Aby określić niestandardowy komunikat o błędzie, Użyj atrybutu.</span><span class="sxs-lookup"><span data-stu-id="228b3-390">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="228b3-391">[Remote] — atrybut</span><span class="sxs-lookup"><span data-stu-id="228b3-391">[Remote] attribute</span></span>

<span data-ttu-id="228b3-392">Ten `[Remote]` atrybut implementuje walidację po stronie klienta, która wymaga wywołania metody na serwerze w celu określenia, czy dane wejściowe pola są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-392">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="228b3-393">Na przykład aplikacja może wymagać sprawdzenia, czy nazwa użytkownika jest już używana.</span><span class="sxs-lookup"><span data-stu-id="228b3-393">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="228b3-394">Aby zaimplementować zdalne sprawdzanie poprawności:</span><span class="sxs-lookup"><span data-stu-id="228b3-394">To implement remote validation:</span></span>

1. <span data-ttu-id="228b3-395">Utwórz metodę akcji dla języka JavaScript do wywołania.</span><span class="sxs-lookup"><span data-stu-id="228b3-395">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="228b3-396">Metoda [zdalna](https://jqueryvalidation.org/remote-method/) walidacji jQuery oczekuje odpowiedzi JSON:</span><span class="sxs-lookup"><span data-stu-id="228b3-396">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="228b3-397">`"true"`oznacza, że dane wejściowe są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-397">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="228b3-398">`"false"`, `undefined` lub `null` oznacza, że dane wejściowe są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-398">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="228b3-399">Wyświetl domyślny komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-399">Display the default error message.</span></span>
   * <span data-ttu-id="228b3-400">Każdy inny ciąg oznacza, że dane wejściowe są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="228b3-400">Any other string means the input is invalid.</span></span> <span data-ttu-id="228b3-401">Wyświetl ciąg jako niestandardowy komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-401">Display the string as a custom error message.</span></span>

   <span data-ttu-id="228b3-402">Oto przykład metody akcji, która zwraca niestandardowy komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-402">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="228b3-403">W klasie modelu Dodaj adnotację do właściwości z atrybutem wskazującym na `[Remote]` metodę akcji walidacji, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-403">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="228b3-404">Ten `[Remote]` atrybut znajduje się w `Microsoft.AspNetCore.Mvc` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="228b3-404">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="228b3-405">Zainstaluj pakiet NuGet [Microsoft. AspNetCore. MVC. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) , jeśli nie używasz programu `Microsoft.AspNetCore.App` lub `Microsoft.AspNetCore.All` pakietu.</span><span class="sxs-lookup"><span data-stu-id="228b3-405">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="228b3-406">Dodatkowe pola</span><span class="sxs-lookup"><span data-stu-id="228b3-406">Additional fields</span></span>

<span data-ttu-id="228b3-407">`AdditionalFields`Właściwość `[Remote]` atrybutu umożliwia Weryfikowanie kombinacji pól względem danych na serwerze.</span><span class="sxs-lookup"><span data-stu-id="228b3-407">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="228b3-408">Na przykład jeśli `User` model ma `FirstName` i `LastName` właściwości, można sprawdzić, czy żaden istniejący użytkownik już ma tę parę nazw.</span><span class="sxs-lookup"><span data-stu-id="228b3-408">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="228b3-409">Poniższy przykład przedstawia sposób użycia `AdditionalFields` :</span><span class="sxs-lookup"><span data-stu-id="228b3-409">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="228b3-410">`AdditionalFields`może być jawnie ustawiona dla ciągów `"FirstName"` i `"LastName"` , ale użycie operatora [nameof](/dotnet/csharp/language-reference/keywords/nameof) upraszcza późniejsze refaktoryzacje.</span><span class="sxs-lookup"><span data-stu-id="228b3-410">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="228b3-411">Metoda akcji dla tej weryfikacji musi akceptować zarówno imiona, jak i nazwiska:</span><span class="sxs-lookup"><span data-stu-id="228b3-411">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="228b3-412">Gdy użytkownik wprowadzi imię lub nazwisko, kod JavaScript wykonuje zdalne wywołanie, aby sprawdzić, czy ta para nazw została podjęta.</span><span class="sxs-lookup"><span data-stu-id="228b3-412">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="228b3-413">Aby sprawdzić poprawność dwóch lub więcej pól, podaj je jako listę rozdzielaną przecinkami.</span><span class="sxs-lookup"><span data-stu-id="228b3-413">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="228b3-414">Na przykład aby dodać `MiddleName` Właściwość do modelu, należy ustawić `[Remote]` atrybut, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-414">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="228b3-415">`AdditionalFields`, podobnie jak wszystkie argumenty atrybutu, musi być wyrażeniem stałym.</span><span class="sxs-lookup"><span data-stu-id="228b3-415">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="228b3-416">W związku z tym nie należy używać [interpolowanego ciągu](/dotnet/csharp/language-reference/keywords/interpolated-strings) ani wywołania <xref:System.String.Join*> do inicjowania `AdditionalFields` .</span><span class="sxs-lookup"><span data-stu-id="228b3-416">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="228b3-417">Alternatywy dla wbudowanych atrybutów</span><span class="sxs-lookup"><span data-stu-id="228b3-417">Alternatives to built-in attributes</span></span>

<span data-ttu-id="228b3-418">Jeśli potrzebujesz weryfikacji niedostarczonej przez wbudowane atrybuty, możesz:</span><span class="sxs-lookup"><span data-stu-id="228b3-418">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="228b3-419">[Tworzenie atrybutów niestandardowych](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="228b3-419">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="228b3-420">[Zaimplementuj IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="228b3-420">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="228b3-421">Atrybuty niestandardowe</span><span class="sxs-lookup"><span data-stu-id="228b3-421">Custom attributes</span></span>

<span data-ttu-id="228b3-422">W przypadku scenariuszy, w których wbudowane atrybuty walidacji nie obsługują, można utworzyć niestandardowe atrybuty walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-422">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="228b3-423">Utwórz klasę, która dziedziczy z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> , i Zastąp <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodę.</span><span class="sxs-lookup"><span data-stu-id="228b3-423">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="228b3-424">`IsValid`Metoda akceptuje obiekt o nazwie *Value*, czyli dane wejściowe do zweryfikowania.</span><span class="sxs-lookup"><span data-stu-id="228b3-424">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="228b3-425">Przeciążenie akceptuje również `ValidationContext` obiekt, który zawiera dodatkowe informacje, takie jak wystąpienie modelu utworzone przez powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-425">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="228b3-426">Poniższy przykład sprawdza, czy Data wydania filmu w *klasycznym* gatunku nie jest późniejsza niż określony rok.</span><span class="sxs-lookup"><span data-stu-id="228b3-426">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="228b3-427">Ten `[ClassicMovie2]` atrybut najpierw sprawdza gatunek i kontynuuje działanie tylko wtedy, gdy jest on *klasyczny*.</span><span class="sxs-lookup"><span data-stu-id="228b3-427">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="228b3-428">W przypadku filmów identyfikowanych jako Classics sprawdza datę wydania, aby upewnić się, że nie jest ona późniejsza niż limit przesłany do konstruktora atrybutu.</span><span class="sxs-lookup"><span data-stu-id="228b3-428">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="228b3-429">`movie`Zmienna w poprzednim przykładzie reprezentuje `Movie` obiekt, który zawiera dane z przesłania formularza.</span><span class="sxs-lookup"><span data-stu-id="228b3-429">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="228b3-430">`IsValid`Metoda sprawdza datę i gatunek.</span><span class="sxs-lookup"><span data-stu-id="228b3-430">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="228b3-431">Po pomyślnej weryfikacji program `IsValid` zwraca `ValidationResult.Success` kod.</span><span class="sxs-lookup"><span data-stu-id="228b3-431">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="228b3-432">Gdy Walidacja nie powiedzie się, `ValidationResult` zostanie zwrócony komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-432">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="228b3-433">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="228b3-433">IValidatableObject</span></span>

<span data-ttu-id="228b3-434">Poprzedni przykład działa tylko z `Movie` typami.</span><span class="sxs-lookup"><span data-stu-id="228b3-434">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="228b3-435">Kolejną opcją weryfikacji na poziomie klasy jest implementacja `IValidatableObject` w klasie modelu, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-435">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="228b3-436">Sprawdzanie poprawności węzła najwyższego poziomu</span><span class="sxs-lookup"><span data-stu-id="228b3-436">Top-level node validation</span></span>

<span data-ttu-id="228b3-437">Węzły najwyższego poziomu obejmują:</span><span class="sxs-lookup"><span data-stu-id="228b3-437">Top-level nodes include:</span></span>

* <span data-ttu-id="228b3-438">Parametry akcji</span><span class="sxs-lookup"><span data-stu-id="228b3-438">Action parameters</span></span>
* <span data-ttu-id="228b3-439">Właściwości kontrolera</span><span class="sxs-lookup"><span data-stu-id="228b3-439">Controller properties</span></span>
* <span data-ttu-id="228b3-440">Parametry procedury obsługi stron</span><span class="sxs-lookup"><span data-stu-id="228b3-440">Page handler parameters</span></span>
* <span data-ttu-id="228b3-441">Właściwości modelu strony</span><span class="sxs-lookup"><span data-stu-id="228b3-441">Page model properties</span></span>

<span data-ttu-id="228b3-442">Węzły najwyższego poziomu powiązane z modelem są weryfikowane jako uzupełnienie właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-442">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="228b3-443">W poniższym przykładzie z przykładowej aplikacji `VerifyPhone` Metoda używa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> do walidacji `phone` parametru Action:</span><span class="sxs-lookup"><span data-stu-id="228b3-443">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="228b3-444">Węzły najwyższego poziomu mogą korzystać <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> z atrybutów walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-444">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="228b3-445">W poniższym przykładzie z przykładowej aplikacji `CheckAge` Metoda określa, że `age` parametr musi być powiązany z ciągu zapytania, gdy formularz zostanie przesłany:</span><span class="sxs-lookup"><span data-stu-id="228b3-445">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="228b3-446">Na stronie sprawdzanie wieku (*Sprawdź. cshtml*) Istnieją dwa formy.</span><span class="sxs-lookup"><span data-stu-id="228b3-446">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="228b3-447">Pierwszy formularz przesyła `Age` wartość `99` jako ciąg zapytania: `https://localhost:5001/Users/CheckAge?Age=99` .</span><span class="sxs-lookup"><span data-stu-id="228b3-447">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="228b3-448">Po przesłaniu poprawnie sformatowanego `age` parametru z ciągu zapytania, formularz sprawdza poprawność.</span><span class="sxs-lookup"><span data-stu-id="228b3-448">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="228b3-449">Drugi formularz na stronie sprawdzanie wieku przesyła `Age` wartość w treści żądania, a Walidacja nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="228b3-449">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="228b3-450">Powiązanie nie powiodło się, ponieważ `age` parametr musi pochodzić z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="228b3-450">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="228b3-451">W przypadku korzystania z programu `CompatibilityVersion.Version_2_1` lub nowszego Walidacja węzła najwyższego poziomu jest domyślnie włączona.</span><span class="sxs-lookup"><span data-stu-id="228b3-451">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="228b3-452">W przeciwnym razie Walidacja węzła najwyższego poziomu jest wyłączona.</span><span class="sxs-lookup"><span data-stu-id="228b3-452">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="228b3-453">Opcję domyślną można przesłonić <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> , ustawiając właściwość w ( `Startup.ConfigureServices` ), jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="228b3-453">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="228b3-454">Maksymalna liczba błędów</span><span class="sxs-lookup"><span data-stu-id="228b3-454">Maximum errors</span></span>

<span data-ttu-id="228b3-455">Walidacja jest zatrzymywana, gdy zostanie osiągnięta maksymalna liczba błędów (domyślnie 200).</span><span class="sxs-lookup"><span data-stu-id="228b3-455">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="228b3-456">Tę liczbę można skonfigurować przy użyciu następującego kodu w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="228b3-456">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="228b3-457">Maksymalna rekursja</span><span class="sxs-lookup"><span data-stu-id="228b3-457">Maximum recursion</span></span>

<span data-ttu-id="228b3-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>przechodzi do grafu obiektów sprawdzanego przez model.</span><span class="sxs-lookup"><span data-stu-id="228b3-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="228b3-459">W przypadku modeli, które są bardzo głębokie lub nieskończonie cykliczne, walidacja może spowodować przepełnienie stosu.</span><span class="sxs-lookup"><span data-stu-id="228b3-459">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="228b3-460">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) umożliwia szybkie zakończenie sprawdzania poprawności, Jeśli rekursja odwiedzających przekracza skonfigurowaną głębokość.</span><span class="sxs-lookup"><span data-stu-id="228b3-460">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="228b3-461">Wartość domyślna `MvcOptions.MaxValidationDepth` to 32 w przypadku uruchamiania z systemem `CompatibilityVersion.Version_2_2` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="228b3-461">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="228b3-462">W przypadku wcześniejszych wersji wartość jest równa null, co oznacza brak ograniczenia głębokości.</span><span class="sxs-lookup"><span data-stu-id="228b3-462">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="228b3-463">Automatyczny krótki obwód</span><span class="sxs-lookup"><span data-stu-id="228b3-463">Automatic short-circuit</span></span>

<span data-ttu-id="228b3-464">Sprawdzanie poprawności jest automatycznie skracane (pomijane), jeśli wykres modelu nie wymaga weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-464">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="228b3-465">Obiekty, dla których środowisko uruchomieniowe pomija sprawdzanie poprawności dla dołączania kolekcji elementów podstawowych (takich jak `byte[]` , `string[]` , `Dictionary<string, string>` ) i złożonych wykresów obiektów, które nie mają żadnych modułów sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="228b3-465">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="228b3-466">Wyłącz weryfikację</span><span class="sxs-lookup"><span data-stu-id="228b3-466">Disable validation</span></span>

<span data-ttu-id="228b3-467">Aby wyłączyć weryfikację:</span><span class="sxs-lookup"><span data-stu-id="228b3-467">To disable validation:</span></span>

1. <span data-ttu-id="228b3-468">Utwórz implementację `IObjectModelValidator` , która nie oznacza żadnych pól jako nieprawidłowych.</span><span class="sxs-lookup"><span data-stu-id="228b3-468">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="228b3-469">Dodaj następujący kod, aby `Startup.ConfigureServices` zastąpić domyślną `IObjectModelValidator` implementację w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="228b3-469">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="228b3-470">Nadal mogą pojawić się błędy stanu modelu pochodzące z powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="228b3-470">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="228b3-471">Weryfikacja po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-471">Client-side validation</span></span>

<span data-ttu-id="228b3-472">Weryfikacja po stronie klienta uniemożliwia przesyłanie, dopóki formularz nie będzie prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="228b3-472">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="228b3-473">Przycisk Prześlij uruchamia kod JavaScript, który przesyła formularz lub wyświetla komunikaty o błędach.</span><span class="sxs-lookup"><span data-stu-id="228b3-473">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="228b3-474">Weryfikacja po stronie klienta umożliwia uniknięcie niepotrzebnej komunikacji z serwerem w przypadku błędów danych wejściowych w formularzu.</span><span class="sxs-lookup"><span data-stu-id="228b3-474">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="228b3-475">Następujący skrypt odwołuje się do *_Layout. cshtml* i *_ValidationScriptsPartial. cshtml* obsługuje walidację po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="228b3-475">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="228b3-476">Skrypt [niezauważalnego sprawdzania poprawności jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) jest niestandardową biblioteką frontonu firmy Microsoft, która kompiluje się w popularnej wersji interfejsu [jQuery](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="228b3-476">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="228b3-477">Bez dyskretnej weryfikacji jQuery należy wykonać kod tej samej logiki walidacji w dwóch miejscach: raz w atrybuty walidacji po stronie serwera we właściwościach modelu, a następnie ponownie w skryptach po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="228b3-477">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="228b3-478">Zamiast tego, [pomocników tagów](xref:mvc/views/tag-helpers/intro) i [pomocników HTML](xref:mvc/views/overview) używają atrybutów walidacji i metadanych typu z właściwości modelu do renderowania atrybutów HTML 5 `data-` dla elementów formularza, które wymagają walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-478">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="228b3-479">niezauważalne sprawdzenie poprawności przez program jQuery umożliwia przeanalizowanie `data-` atrybutów i przekazanie logiki do programu jQuery Validate, efektywne "Kopiowanie" logiki walidacji po stronie serwera do klienta.</span><span class="sxs-lookup"><span data-stu-id="228b3-479">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="228b3-480">Błędy sprawdzania poprawności można wyświetlić na kliencie przy użyciu pomocników tagów, jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="228b3-480">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="228b3-481">Poprzednie pomocnicy tagów renderują następujący kod HTML.</span><span class="sxs-lookup"><span data-stu-id="228b3-481">The preceding tag helpers render the following HTML.</span></span>

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

<span data-ttu-id="228b3-482">Zauważ, że `data-` atrybuty w danych wyjściowych HTML odpowiadają atrybutom walidacji `ReleaseDate` właściwości.</span><span class="sxs-lookup"><span data-stu-id="228b3-482">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="228b3-483">Ten `data-val-required` atrybut zawiera komunikat o błędzie, który zostanie wyświetlony, jeśli użytkownik nie wypełni pola Data wydania.</span><span class="sxs-lookup"><span data-stu-id="228b3-483">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="228b3-484">niezauważalne sprawdzenie poprawności przez funkcję jQuery powoduje, że ta wartość jest przekazywana do walidacji [wymaganej metody ()](https://jqueryvalidation.org/required-method/) , która następnie wyświetla ten komunikat w elemencie towarzyszącym **\<span>** .</span><span class="sxs-lookup"><span data-stu-id="228b3-484">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="228b3-485">Walidacja typu danych jest oparta na typie .NET właściwości, chyba że zostanie zastąpiona przez `[DataType]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="228b3-485">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="228b3-486">Przeglądarki mają własne domyślne komunikaty o błędach, ale pakietem weryfikacji jQuery nie dyskretnego sprawdzania poprawności może przesłonić te komunikaty.</span><span class="sxs-lookup"><span data-stu-id="228b3-486">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="228b3-487">`[DataType]`atrybuty i podklasy, takie jak `[EmailAddress]` pozwalają określić komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="228b3-487">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="228b3-488">Dodawanie walidacji do formularzy dynamicznych</span><span class="sxs-lookup"><span data-stu-id="228b3-488">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="228b3-489">w przypadku niedyskretnego sprawdzania poprawności jest sprawdzana logika walidacji i parametry do jQuery podczas pierwszego ładowania strony.</span><span class="sxs-lookup"><span data-stu-id="228b3-489">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="228b3-490">W związku z tym sprawdzanie poprawności nie działa automatycznie na formularzach generowanych dynamicznie.</span><span class="sxs-lookup"><span data-stu-id="228b3-490">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="228b3-491">Aby włączyć weryfikację, poinformuj jQuery o niezauważalnej weryfikacji, aby przeanalizować formularz dynamiczny bezpośrednio po jego utworzeniu.</span><span class="sxs-lookup"><span data-stu-id="228b3-491">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="228b3-492">Na przykład poniższy kod konfiguruje walidację po stronie klienta w formularzu dodanym przez AJAX.</span><span class="sxs-lookup"><span data-stu-id="228b3-492">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="228b3-493">`$.validator.unobtrusive.parse()`Metoda akceptuje selektor jQuery dla jednego argumentu.</span><span class="sxs-lookup"><span data-stu-id="228b3-493">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="228b3-494">Ta metoda informuje niedyskretną weryfikację jQuery do analizy `data-` atrybutów formularzy w ramach tego selektora.</span><span class="sxs-lookup"><span data-stu-id="228b3-494">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="228b3-495">Wartości tych atrybutów są następnie przesyłane do wtyczki do walidacji jQuery.</span><span class="sxs-lookup"><span data-stu-id="228b3-495">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="228b3-496">Dodawanie walidacji do formantów dynamicznych</span><span class="sxs-lookup"><span data-stu-id="228b3-496">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="228b3-497">`$.validator.unobtrusive.parse()`Metoda działa na całym formularzu, a nie na poszczególnych dynamicznie generowanych kontrolkach, takich jak `<input>` i `<select/>` .</span><span class="sxs-lookup"><span data-stu-id="228b3-497">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="228b3-498">Aby przeanalizować formularz, Usuń dane sprawdzania poprawności, które zostały dodane, gdy formularz został wcześniej przeanalizowany, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-498">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="228b3-499">Niestandardowe sprawdzanie poprawności po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-499">Custom client-side validation</span></span>

<span data-ttu-id="228b3-500">Niestandardowe sprawdzanie poprawności po stronie klienta jest wykonywane przez generowanie `data-` atrybutów HTML, które działają z niestandardowym identyfikatorem platformy jQuery.</span><span class="sxs-lookup"><span data-stu-id="228b3-500">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="228b3-501">Następujący przykładowy kod karty został zapisany dla `ClassicMovie` `ClassicMovie2` atrybutów i, które zostały wprowadzone wcześniej w tym artykule:</span><span class="sxs-lookup"><span data-stu-id="228b3-501">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="228b3-502">Aby uzyskać informacje o sposobach pisania kart sieciowych, zobacz [dokumentację dotyczącą platformy jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="228b3-502">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="228b3-503">Użycie karty dla danego pola jest wyzwalane przez `data-` atrybuty, które:</span><span class="sxs-lookup"><span data-stu-id="228b3-503">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="228b3-504">Oznacz pole jako podlegające walidacji ( `data-val="true"` ).</span><span class="sxs-lookup"><span data-stu-id="228b3-504">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="228b3-505">Zidentyfikuj nazwę reguły walidacji i tekst komunikatu o błędzie (na przykład `data-val-rulename="Error message."` ).</span><span class="sxs-lookup"><span data-stu-id="228b3-505">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="228b3-506">Podaj wszelkie dodatkowe parametry wymagane przez moduł sprawdzania poprawności (na przykład `data-val-rulename-parm1="value"` ).</span><span class="sxs-lookup"><span data-stu-id="228b3-506">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="228b3-507">W poniższym przykładzie przedstawiono `data-` atrybuty dla atrybutu przykładowej aplikacji `ClassicMovie` :</span><span class="sxs-lookup"><span data-stu-id="228b3-507">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="228b3-508">Jak wspomniano wcześniej, [pomocników tagów](xref:mvc/views/tag-helpers/intro) i [pomocników HTML](xref:mvc/views/overview) wykorzystują informacje z atrybutów walidacji do renderowania `data-` atrybutów.</span><span class="sxs-lookup"><span data-stu-id="228b3-508">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="228b3-509">Istnieją dwie opcje pisania kodu, które powoduje tworzenie niestandardowych `data-` atrybutów HTML:</span><span class="sxs-lookup"><span data-stu-id="228b3-509">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="228b3-510">Utwórz klasę, która pochodzi od `AttributeAdapterBase<TAttribute>` i klasy implementującej `IValidationAttributeAdapterProvider` , i Zarejestruj swój atrybut oraz jego kartę w programie di.</span><span class="sxs-lookup"><span data-stu-id="228b3-510">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="228b3-511">Ta metoda jest zgodna z [pojedynczym podmiotem odpowiedzialnym](https://wikipedia.org/wiki/Single_responsibility_principle) w odniesieniu do kodu weryfikacyjnego związanego z serwerem i klienta jest w osobnych klasach.</span><span class="sxs-lookup"><span data-stu-id="228b3-511">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="228b3-512">Adapter ma również zalety, że ponieważ jest on zarejestrowany w programie DI, w razie potrzeby są dostępne inne usługi w programie DI.</span><span class="sxs-lookup"><span data-stu-id="228b3-512">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="228b3-513">Zaimplementuj `IClientModelValidator` w `ValidationAttribute` klasie.</span><span class="sxs-lookup"><span data-stu-id="228b3-513">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="228b3-514">Ta metoda może być odpowiednia, jeśli atrybut nie wykonuje walidacji po stronie serwera i nie wymaga żadnych usług z programu DI.</span><span class="sxs-lookup"><span data-stu-id="228b3-514">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="228b3-515">AttributeAdapter dla weryfikacji po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-515">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="228b3-516">Ta metoda renderowania `data-` atrybutów w kodzie HTML jest używana przez `ClassicMovie` atrybut w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-516">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="228b3-517">Aby dodać weryfikację klienta przy użyciu tej metody:</span><span class="sxs-lookup"><span data-stu-id="228b3-517">To add client validation by using this method:</span></span>

1. <span data-ttu-id="228b3-518">Utwórz klasę adaptera atrybutów dla niestandardowego atrybutu walidacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-518">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="228b3-519">Utwórz klasę z [AttributeAdapterBase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="228b3-519">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="228b3-520">Utwórz `AddValidation` metodę, która dodaje `data-` atrybuty do renderowanych danych wyjściowych, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-520">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="228b3-521">Utwórz klasę dostawcy kart implementującą <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> .</span><span class="sxs-lookup"><span data-stu-id="228b3-521">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="228b3-522">W `GetAttributeAdapter` metodzie Przekaż atrybut niestandardowy do konstruktora adaptera, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-522">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="228b3-523">Zarejestruj dostawcę karty dla DI in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="228b3-523">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="228b3-524">IClientModelValidator dla weryfikacji po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-524">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="228b3-525">Ta metoda renderowania `data-` atrybutów w kodzie HTML jest używana przez `ClassicMovie2` atrybut w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="228b3-525">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="228b3-526">Aby dodać weryfikację klienta przy użyciu tej metody:</span><span class="sxs-lookup"><span data-stu-id="228b3-526">To add client validation by using this method:</span></span>

* <span data-ttu-id="228b3-527">W atrybucie niestandardowego sprawdzania poprawności Zaimplementuj `IClientModelValidator` interfejs i Utwórz `AddValidation` metodę.</span><span class="sxs-lookup"><span data-stu-id="228b3-527">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="228b3-528">W `AddValidation` metodzie Dodaj `data-` atrybuty do walidacji, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="228b3-528">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="228b3-529">Wyłącz weryfikację po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="228b3-529">Disable client-side validation</span></span>

<span data-ttu-id="228b3-530">Poniższy kod wyłącza weryfikację klienta w widokach MVC:</span><span class="sxs-lookup"><span data-stu-id="228b3-530">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="228b3-531">I na Razor stronach:</span><span class="sxs-lookup"><span data-stu-id="228b3-531">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="228b3-532">Kolejną opcją wyłączenia sprawdzania poprawności klienta jest komentarz do odwołania do `_ValidationScriptsPartial` w pliku *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="228b3-532">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="228b3-533">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="228b3-533">Additional resources</span></span>

* [<span data-ttu-id="228b3-534">Przestrzeń nazw System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="228b3-534">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="228b3-535">Powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="228b3-535">Model Binding</span></span>](model-binding.md)

::: moniker-end
