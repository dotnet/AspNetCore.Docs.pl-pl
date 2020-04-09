---
title: Globalizacja i lokalizacja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core zapewnia usługi i oprogramowanie pośredniczące do lokalizowania zawartości w różnych językach i kulturach.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: b175354220a8a71c029e005f27443d5a72749a11
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662121"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="bc10d-103">Globalizacja i lokalizacja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc10d-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="bc10d-104">Rick [Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), Bart [Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)i [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="bc10d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="bc10d-105">Utworzenie wielojęzycznej witryny z ASP.NET Core pozwoli Twojej witrynie dotrzeć do szerszego grona odbiorców.</span><span class="sxs-lookup"><span data-stu-id="bc10d-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="bc10d-106">ASP.NET Core zapewnia usługi i oprogramowanie pośredniczące do lokalizacji w różnych językach i kulturach.</span><span class="sxs-lookup"><span data-stu-id="bc10d-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="bc10d-107">Internacjonalizacja obejmuje [globalizację](/dotnet/api/system.globalization) i [lokalizację.](/dotnet/standard/globalization-localization/localization)</span><span class="sxs-lookup"><span data-stu-id="bc10d-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="bc10d-108">Globalizacja to proces projektowania aplikacji, które obsługują różne kultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="bc10d-109">Globalizacja dodaje obsługę danych wejściowych, wyświetlanych i wyjściowych zdefiniowanego zestawu skryptów językowych, które odnoszą się do określonych obszarów geograficznych.</span><span class="sxs-lookup"><span data-stu-id="bc10d-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="bc10d-110">Lokalizacja to proces dostosowywania zglobalizowanej aplikacji, która została już przetworzona pod kątem lokalizacji, do określonej kultury/ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="bc10d-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="bc10d-111">Aby uzyskać więcej informacji, zobacz **terminy globalizacji i lokalizacji** na końcu tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="bc10d-112">Lokalizacja aplikacji obejmuje następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="bc10d-112">App localization involves the following:</span></span>

1. <span data-ttu-id="bc10d-113">Umiejscowienie zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="bc10d-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="bc10d-114">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="bc10d-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="bc10d-115">Implementowanie strategii wyboru języka/kultury dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="bc10d-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="bc10d-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bc10d-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="bc10d-117">Umiejscowienie zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="bc10d-117">Make the app's content localizable</span></span>

<span data-ttu-id="bc10d-118">Wprowadzony w ASP.NET Core `IStringLocalizer` i `IStringLocalizer<T>` zostały zaprojektowany w celu zwiększenia wydajności podczas tworzenia zlokalizowanych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bc10d-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="bc10d-119">`IStringLocalizer`używa [ResourceManager](/dotnet/api/system.resources.resourcemanager) i [ResourceReader](/dotnet/api/system.resources.resourcereader) do zapewnienia zasobów specyficznych dla kultury w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="bc10d-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="bc10d-120">Prosty interfejs ma indeksatora `IEnumerable` i do zwracania zlokalizowanych ciągów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="bc10d-121">`IStringLocalizer`nie wymaga przechowywania domyślnych ciągów języka w pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="bc10d-122">Można opracować aplikację przeznaczoną do lokalizacji i nie trzeba tworzyć plików zasobów na wczesnym etapie rozwoju.</span><span class="sxs-lookup"><span data-stu-id="bc10d-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="bc10d-123">Poniższy kod pokazuje, jak zawinąć ciąg "O tytule" dla lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="bc10d-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="bc10d-124">W powyższym kodzie `IStringLocalizer<T>` implementacja pochodzi z [iniekcji zależności](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="bc10d-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="bc10d-125">Jeśli zlokalizowana wartość "O tytule" nie zostanie znaleziona, zwracany jest klucz indeksatora, czyli ciąg "O tytule".</span><span class="sxs-lookup"><span data-stu-id="bc10d-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="bc10d-126">Można pozostawić domyślne ciągi literał języka w aplikacji i zawinąć je w lokalizatorze, dzięki czemu można skupić się na tworzeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bc10d-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="bc10d-127">Programujesz aplikację przy pracy w domyślnym języku i przygotowujesz ją do kroku lokalizacji bez uprzedniego utworzenia domyślnego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="bc10d-128">Alternatywnie można użyć tradycyjnego podejścia i podać klucz do pobrania domyślnego ciągu języka.</span><span class="sxs-lookup"><span data-stu-id="bc10d-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="bc10d-129">Dla wielu deweloperów nowy przepływ pracy nie ma domyślnego pliku *.resx* języka i po prostu zawijania literałów ciąg może zmniejszyć obciążenie związane z lokalizowaniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bc10d-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="bc10d-130">Inni deweloperzy wolą tradycyjny przepływ pracy, ponieważ może to ułatwić pracę z dłuższymi literałami ciągów i ułatwić aktualizowanie zlokalizowanych ciągów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="bc10d-131">Użyj `IHtmlLocalizer<T>` implementacji dla zasobów, które zawierają html.</span><span class="sxs-lookup"><span data-stu-id="bc10d-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="bc10d-132">`IHtmlLocalizer`HTML koduje argumenty, które są sformatowane w ciągu zasobu, ale nie koduje html samego ciągu zasobu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="bc10d-133">W przykładzie wyróżnionym poniżej `name` tylko wartość parametru jest zakodowana w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="bc10d-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="bc10d-134">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie html.</span><span class="sxs-lookup"><span data-stu-id="bc10d-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="bc10d-135">Na najniższym poziomie, `IStringLocalizerFactory` można wyjść z [iniekcji zależności:](dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="bc10d-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="bc10d-136">Powyższy kod pokazuje każdą z dwóch metod tworzenia fabrycznego.</span><span class="sxs-lookup"><span data-stu-id="bc10d-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="bc10d-137">Można podzielić zlokalizowane ciągi według kontrolera, obszaru lub mieć tylko jeden kontener.</span><span class="sxs-lookup"><span data-stu-id="bc10d-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="bc10d-138">W przykładowej aplikacji klasy fikcyjnej o nazwie `SharedResource` jest używany dla zasobów udostępnionych.</span><span class="sxs-lookup"><span data-stu-id="bc10d-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="bc10d-139">Niektórzy deweloperzy `Startup` używają tej klasy do zawierania ciągów globalnych lub udostępnionych.</span><span class="sxs-lookup"><span data-stu-id="bc10d-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="bc10d-140">W poniższym przykładzie stosuje się `InfoController` lokalizatory: `SharedResource`</span><span class="sxs-lookup"><span data-stu-id="bc10d-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="bc10d-141">Zobacz lokalizację</span><span class="sxs-lookup"><span data-stu-id="bc10d-141">View localization</span></span>

<span data-ttu-id="bc10d-142">Usługa `IViewLocalizer` udostępnia zlokalizowane ciągi dla [widoku](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="bc10d-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="bc10d-143">Klasa `ViewLocalizer` implementuje ten interfejs i znajduje lokalizację zasobu ze ścieżki pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="bc10d-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="bc10d-144">Poniższy kod pokazuje, jak używać `IViewLocalizer`domyślnej implementacji:</span><span class="sxs-lookup"><span data-stu-id="bc10d-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="bc10d-145">Domyślna implementacja `IViewLocalizer` znajduje plik zasobu na podstawie nazwy pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="bc10d-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="bc10d-146">Nie ma opcji użycia globalnego udostępnionego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="bc10d-147">`ViewLocalizer`implementuje localizer `IHtmlLocalizer`przy użyciu , więc Razor nie koduje HTML zlokalizowane ciąg.</span><span class="sxs-lookup"><span data-stu-id="bc10d-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="bc10d-148">Można sparametryzować ciągi zasobów i `IViewLocalizer` kodować parametry HTML, ale nie ciąg zasobu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="bc10d-149">Należy wziąć pod uwagę następujące znaczniki Razor:</span><span class="sxs-lookup"><span data-stu-id="bc10d-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="bc10d-150">Francuski plik zasobów może zawierać następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="bc10d-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="bc10d-151">Klucz</span><span class="sxs-lookup"><span data-stu-id="bc10d-151">Key</span></span> | <span data-ttu-id="bc10d-152">Wartość</span><span class="sxs-lookup"><span data-stu-id="bc10d-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="bc10d-153">Renderowany widok będzie zawierał znaczniki HTML z pliku zasobu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="bc10d-154">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie html.</span><span class="sxs-lookup"><span data-stu-id="bc10d-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="bc10d-155">Aby użyć udostępnionego pliku zasobu `IHtmlLocalizer<T>`w widoku, wstrzyknąć:</span><span class="sxs-lookup"><span data-stu-id="bc10d-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="bc10d-156">Lokalizacjaanotacji danych</span><span class="sxs-lookup"><span data-stu-id="bc10d-156">DataAnnotations localization</span></span>

<span data-ttu-id="bc10d-157">Komunikaty o błędach DataAnnotations są zlokalizowane za pomocą `IStringLocalizer<T>`pliku .</span><span class="sxs-lookup"><span data-stu-id="bc10d-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="bc10d-158">Korzystając z `ResourcesPath = "Resources"`tej opcji, `RegisterViewModel` komunikaty o błędach mogą być przechowywane w jednej z następujących ścieżek:</span><span class="sxs-lookup"><span data-stu-id="bc10d-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="bc10d-159">*Zasoby/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="bc10d-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="bc10d-160">*Zasoby/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="bc10d-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="bc10d-161">W ASP.NET Core MVC 1.1.0 i nowsze, atrybuty nie sprawdzania poprawności są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="bc10d-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="bc10d-162">ASP.NET Core MVC 1.0 **nie** wyszukuje zlokalizowanych ciągów atrybutów nieuwzględniania.</span><span class="sxs-lookup"><span data-stu-id="bc10d-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="bc10d-163">Używanie jednego ciągu zasobu dla wielu klas</span><span class="sxs-lookup"><span data-stu-id="bc10d-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="bc10d-164">Poniższy kod pokazuje, jak używać jednego ciągu zasobu dla atrybutów sprawdzania poprawności z wieloma klasami:</span><span class="sxs-lookup"><span data-stu-id="bc10d-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="bc10d-165">W poprzednim kodzie `SharedResource` jest klasa odpowiadająca resx, gdzie są przechowywane komunikaty sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="bc10d-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="bc10d-166">Dzięki takiemu podejściu dataannotations będzie używać `SharedResource`tylko , a nie zasobu dla każdej klasy.</span><span class="sxs-lookup"><span data-stu-id="bc10d-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="bc10d-167">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="bc10d-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="bc10d-168">Wspieranekultury i wspieranekultury</span><span class="sxs-lookup"><span data-stu-id="bc10d-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="bc10d-169">ASP.NET Core umożliwia określenie dwóch wartości `SupportedCultures` kultury `SupportedUICultures`i .</span><span class="sxs-lookup"><span data-stu-id="bc10d-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="bc10d-170">[CultureInfo](/dotnet/api/system.globalization.cultureinfo) Obiekt `SupportedCultures` dla określa wyniki funkcji zależnych od kultury, takich jak data, godzina, liczba i formatowanie waluty.</span><span class="sxs-lookup"><span data-stu-id="bc10d-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="bc10d-171">`SupportedCultures`określa również kolejność sortowania tekstu, konwencje wielkości liter i porównania ciągów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="bc10d-172">Zobacz [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) aby uzyskać więcej informacji na temat sposobu serwera pobiera kultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="bc10d-173">Określa, `SupportedUICultures` które tłumaczy ciągi (z plików *resx)* są wyszukane przez [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="bc10d-173">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="bc10d-174">Po `ResourceManager` prostu wyszukuje ciągi specyficzne `CurrentUICulture`dla kultury, które są określane przez .</span><span class="sxs-lookup"><span data-stu-id="bc10d-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="bc10d-175">Każdy wątek `CurrentCulture` w `CurrentUICulture` .NET ma i obiektów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="bc10d-176">ASP.NET Core sprawdza te wartości podczas renderowania funkcji zależnych od kultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="bc10d-177">Na przykład jeśli kultura bieżącego wątku jest ustawiona na "en-US" (angielski, Stany Zjednoczone), `DateTime.Now.ToLongDateString()` wyświetla "Czwartek, 18 lutego 2016", ale jeśli `CurrentCulture` jest ustawiona na "es-ES" (hiszpański, Hiszpania), wyjście będzie "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="bc10d-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="bc10d-178">Pliki zasobów</span><span class="sxs-lookup"><span data-stu-id="bc10d-178">Resource files</span></span>

<span data-ttu-id="bc10d-179">Plik zasobów jest przydatnym mechanizmem oddzielania ciągów zlokalizowanych od kodu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="bc10d-180">Przetłumaczone ciągi dla języka nie domyślnego są izolowane pliki zasobów *.resx.*</span><span class="sxs-lookup"><span data-stu-id="bc10d-180">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="bc10d-181">Na przykład można utworzyć hiszpański plik zasobów o nazwie *Welcome.es.resx* zawierający przetłumaczone ciągi.</span><span class="sxs-lookup"><span data-stu-id="bc10d-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="bc10d-182">"es" to kod języka hiszpańskiego.</span><span class="sxs-lookup"><span data-stu-id="bc10d-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="bc10d-183">Aby utworzyć ten plik zasobu w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="bc10d-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="bc10d-184">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder, który będzie zawierał plik zasobu > **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="bc10d-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Zagnieżdżone menu kontekstowe: W Eksploratorze rozwiązań menu kontekstowe jest otwarte dla zasobów.](localization/_static/newi.png)

2. <span data-ttu-id="bc10d-187">W polu **Wyszukaj zainstalowane szablony** wprowadź "zasób" i nazwij plik.</span><span class="sxs-lookup"><span data-stu-id="bc10d-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Okno dialogowe Dodawanie nowego elementu](localization/_static/res.png)

3. <span data-ttu-id="bc10d-189">Wprowadź wartość klucza (ciąg macierzysty) w kolumnie **Nazwa** i przetłumaczonym ciągu w kolumnie **Wartość.**</span><span class="sxs-lookup"><span data-stu-id="bc10d-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Plik Welcome.es.resx (plik zasobów powitalnych dla języka hiszpańskiego) ze słowem Hello w kolumnie Nazwa i słowem Hola (Hello in Spanish) w kolumnie Wartość](localization/_static/hola.png)

    <span data-ttu-id="bc10d-191">Program Visual Studio pokazuje plik *Welcome.es.resx.*</span><span class="sxs-lookup"><span data-stu-id="bc10d-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Eksplorator rozwiązań z plikiem zasobów Języka Hiszpańskiego powitanego (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="bc10d-193">Nazewnictwo plików zasobów</span><span class="sxs-lookup"><span data-stu-id="bc10d-193">Resource file naming</span></span>

<span data-ttu-id="bc10d-194">Zasoby są nazwane dla pełnej nazwy typu ich klasy minus nazwa zestawu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="bc10d-195">Na przykład francuski zasób w projekcie, którego głównym zestawem jest `LocalizationWebsite.Web.dll` dla klasy, `LocalizationWebsite.Web.Startup` będzie miał nazwę *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="bc10d-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="bc10d-196">Zasób dla `LocalizationWebsite.Web.Controllers.HomeController` klasy będzie nazwany *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="bc10d-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="bc10d-197">Jeśli obszar nazw klasy docelowej nie jest taki sam jak nazwa zestawu, będziesz potrzebować pełnej nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="bc10d-198">Na przykład w przykładowym projekcie zasób dla tego typu `ExtraNamespace.Tools` będzie miał nazwę *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="bc10d-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="bc10d-199">W przykładowym projekcie `ConfigureServices` metoda `ResourcesPath` ustawia na "Zasoby", więc ścieżka względna projektu dla francuskiego pliku zasobów kontrolera macierzystego to *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="bc10d-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="bc10d-200">Alternatywnie można użyć folderów do organizowania plików zasobów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="bc10d-201">Dla kontrolera domowego ścieżką będą *zasoby/kontrolery/strona GłównaController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="bc10d-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="bc10d-202">Jeśli nie użyjesz `ResourcesPath` tej opcji, plik *.resx* przejdzie do katalogu podstawowego projektu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="bc10d-203">Plik zasobu `HomeController` będzie miał nazwę *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="bc10d-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="bc10d-204">Wybór konwencji nazewnictwa kropki lub ścieżki zależy od sposobu organizowania plików zasobów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="bc10d-205">Nazwa zasobu</span><span class="sxs-lookup"><span data-stu-id="bc10d-205">Resource name</span></span> | <span data-ttu-id="bc10d-206">Nazewnictwo kropki lub ścieżki</span><span class="sxs-lookup"><span data-stu-id="bc10d-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="bc10d-207">Zasoby/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="bc10d-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="bc10d-208">Dot</span><span class="sxs-lookup"><span data-stu-id="bc10d-208">Dot</span></span>  |
| <span data-ttu-id="bc10d-209">Zasoby/Kontrolery/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="bc10d-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="bc10d-210">Ścieżka</span><span class="sxs-lookup"><span data-stu-id="bc10d-210">Path</span></span> |
|    |     |

<span data-ttu-id="bc10d-211">Pliki zasobów `@inject IViewLocalizer` używające w widokach Razor mają podobny wzorzec.</span><span class="sxs-lookup"><span data-stu-id="bc10d-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="bc10d-212">Plik zasobów dla widoku można nazwać przy użyciu nazewnictwa kropki lub nazewnictwa ścieżek.</span><span class="sxs-lookup"><span data-stu-id="bc10d-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="bc10d-213">Razor zobacz pliki zasobów naśladować ścieżkę ich skojarzonego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="bc10d-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="bc10d-214">Zakładając, że `ResourcesPath` ustawimy na "Zasoby", francuski plik zasobów skojarzony z widokiem *Views/Home/About.cshtml* może być jednym z następujących:</span><span class="sxs-lookup"><span data-stu-id="bc10d-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="bc10d-215">Zasoby/Widoki/Strona główna/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="bc10d-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="bc10d-216">Zasoby/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="bc10d-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="bc10d-217">Jeśli nie użyjesz `ResourcesPath` tej opcji, plik *.resx* dla widoku będzie znajdować się w tym samym folderze co widok.</span><span class="sxs-lookup"><span data-stu-id="bc10d-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="bc10d-218">Atrybut RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="bc10d-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="bc10d-219">Atrybut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) udostępnia główny obszar nazw zestawu, gdy główna przestrzeń nazw zestawu jest inna niż nazwa zestawu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="bc10d-220">Może to nastąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="bc10d-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="bc10d-221">Na `my-project-name.csproj` przykład użyje głównego `my_project_name` obszaru nazw `my-project-name` i nazwy zestawu prowadzącego do tego błędu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="bc10d-222">Jeśli główna przestrzeń nazw zestawu jest inna niż nazwa zestawu:</span><span class="sxs-lookup"><span data-stu-id="bc10d-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="bc10d-223">Lokalizacja nie działa domyślnie.</span><span class="sxs-lookup"><span data-stu-id="bc10d-223">Localization does not work by default.</span></span>
* <span data-ttu-id="bc10d-224">Lokalizacja kończy się niepowodzeniem ze względu na sposób, w jaki zasoby są przeszukiwane w ramach zestawu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="bc10d-225">`RootNamespace`jest wartością czasu kompilacji, która nie jest dostępna dla procesu wykonywania.</span><span class="sxs-lookup"><span data-stu-id="bc10d-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="bc10d-226">Jeśli `RootNamespace` jest inny `AssemblyName`niż , należy uwzględnić następujące w *AssemblyInfo.cs* (z wartościami parametrów zastąpionymi wartościami rzeczywistymi):</span><span class="sxs-lookup"><span data-stu-id="bc10d-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="bc10d-227">Poprzedni kod umożliwia pomyślne rozpoznawanie plików resx.</span><span class="sxs-lookup"><span data-stu-id="bc10d-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="bc10d-228">Zachowanie rezerwowe kultury</span><span class="sxs-lookup"><span data-stu-id="bc10d-228">Culture fallback behavior</span></span>

<span data-ttu-id="bc10d-229">Podczas wyszukiwania zasobu, lokalizacja angażuje się w "kultury rezerwy".</span><span class="sxs-lookup"><span data-stu-id="bc10d-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="bc10d-230">Począwszy od żądanej kultury, jeśli nie znaleziono, powraca do kultury nadrzędnej tej kultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="bc10d-231">Na marginesie [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) właściwość reprezentuje kultury nadrzędnej.</span><span class="sxs-lookup"><span data-stu-id="bc10d-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="bc10d-232">Zwykle (ale nie zawsze) oznacza usunięcie krajowego oznaczenia z ISO.</span><span class="sxs-lookup"><span data-stu-id="bc10d-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="bc10d-233">Na przykład dialekt języka hiszpańskiego używany w Meksyku to "es-MX".</span><span class="sxs-lookup"><span data-stu-id="bc10d-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="bc10d-234">Ma rodzica "es"&mdash;hiszpański niespecyfyjniej dla każdego kraju.</span><span class="sxs-lookup"><span data-stu-id="bc10d-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="bc10d-235">Wyobraź sobie, że Twoja witryna otrzymuje żądanie zasobu "Powitalnego" przy użyciu kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="bc10d-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="bc10d-236">System lokalizacji wyszukuje następujące zasoby w kolejności i wybiera pierwsze dopasowanie:</span><span class="sxs-lookup"><span data-stu-id="bc10d-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="bc10d-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="bc10d-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="bc10d-238">*Witamy.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="bc10d-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="bc10d-239">*Welcome.resx* (jeśli `NeutralResourcesLanguage` jest "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="bc10d-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="bc10d-240">Na przykład po usunięciu ".fr" kultury projektowania i masz kultury ustawionej na francuski, domyślny plik zasobów jest odczytywany i ciągi są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="bc10d-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="bc10d-241">Menedżer zasobów wyznacza domyślny lub rezerwowy zasób, gdy nic nie spełnia żądanej kultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="bc10d-242">Jeśli chcesz po prostu zwrócić klucz, gdy brakuje zasobu dla żądanej kultury nie może mieć domyślny plik zasobu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="bc10d-243">Generowanie plików zasobów za pomocą programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc10d-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="bc10d-244">Jeśli utworzysz plik zasobów w programie Visual Studio bez kultury w nazwie pliku (na przykład *Welcome.resx),* Visual Studio utworzy klasę C# z właściwością dla każdego ciągu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="bc10d-245">To zwykle nie jest to, co chcesz z ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bc10d-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="bc10d-246">Zazwyczaj nie masz domyślnego pliku zasobów *resx* (pliku *resx* bez nazwy kultury).</span><span class="sxs-lookup"><span data-stu-id="bc10d-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="bc10d-247">Zalecamy utworzenie pliku *.resx* o nazwie kultury (na przykład *Welcome.fr.resx).*</span><span class="sxs-lookup"><span data-stu-id="bc10d-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="bc10d-248">Podczas tworzenia pliku *.resx* o nazwie kultury program Visual Studio nie wygeneruje pliku klasy.</span><span class="sxs-lookup"><span data-stu-id="bc10d-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="bc10d-249">Dodawanie innych kultur</span><span class="sxs-lookup"><span data-stu-id="bc10d-249">Add other cultures</span></span>

<span data-ttu-id="bc10d-250">Każda kombinacja języka i kultury (inna niż domyślny język) wymaga unikatowego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="bc10d-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="bc10d-251">Pliki zasobów dla różnych kultur i ustawień regionalnych można tworzyć, tworząc nowe pliki zasobów, w których kody języków ISO są częścią nazwy pliku (na przykład **en-us**, **fr-ca**i **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="bc10d-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="bc10d-252">Te kody ISO są umieszczane między nazwą pliku a rozszerzeniem pliku *.resx,* jak w *Welcome.es-MX.resx* (hiszpański/Meksyk).</span><span class="sxs-lookup"><span data-stu-id="bc10d-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="bc10d-253">Implementowanie strategii wyboru języka/kultury dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="bc10d-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="bc10d-254">Konfigurowanie lokalizacji</span><span class="sxs-lookup"><span data-stu-id="bc10d-254">Configure localization</span></span>

<span data-ttu-id="bc10d-255">Lokalizacja jest konfigurowana `Startup.ConfigureServices` w metodzie:</span><span class="sxs-lookup"><span data-stu-id="bc10d-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="bc10d-256">`AddLocalization`Dodaje usługi lokalizacji do kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="bc10d-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="bc10d-257">Powyższy kod ustawia również ścieżkę zasobów na "Zasoby".</span><span class="sxs-lookup"><span data-stu-id="bc10d-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="bc10d-258">`AddViewLocalization`Dodaje obsługę zlokalizowanych plików widoku.</span><span class="sxs-lookup"><span data-stu-id="bc10d-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="bc10d-259">W tym przykładowym widoku lokalizacja jest oparta na sufiksie pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="bc10d-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="bc10d-260">Na przykład "fr" w pliku *Index.fr.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="bc10d-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="bc10d-261">`AddDataAnnotationsLocalization`Dodaje obsługę zlokalizowanych komunikatów `DataAnnotations` sprawdzania poprawności za pośrednictwem `IStringLocalizer` abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="bc10d-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="bc10d-262">Oprogramowanie pośredniczące lokalizacji</span><span class="sxs-lookup"><span data-stu-id="bc10d-262">Localization middleware</span></span>

<span data-ttu-id="bc10d-263">Bieżąca kultura na żądanie jest ustawiona w oprogramowaniu [pośredniczącym](xref:fundamentals/middleware/index)lokalizacji .</span><span class="sxs-lookup"><span data-stu-id="bc10d-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="bc10d-264">Oprogramowanie pośredniczące lokalizacji jest `Startup.Configure` włączone w metodzie.</span><span class="sxs-lookup"><span data-stu-id="bc10d-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="bc10d-265">Oprogramowanie pośredniczące lokalizacji musi być skonfigurowane przed oprogramowaniem pośredniczącym, `app.UseMvcWithDefaultRoute()`które może sprawdzić kulturę żądania (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="bc10d-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="bc10d-266">`UseRequestLocalization`inicjuje `RequestLocalizationOptions` obiekt.</span><span class="sxs-lookup"><span data-stu-id="bc10d-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="bc10d-267">Na każde żądanie `RequestCultureProvider` lista `RequestLocalizationOptions` w jest wyliczona i pierwszy dostawca, który może pomyślnie określić kultury żądania jest używany.</span><span class="sxs-lookup"><span data-stu-id="bc10d-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="bc10d-268">Dostawcy domyślni pochodzą `RequestLocalizationOptions` z klasy:</span><span class="sxs-lookup"><span data-stu-id="bc10d-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="bc10d-269">Lista domyślna przechodzi od najbardziej specyficznego do najmniej specyficznego.</span><span class="sxs-lookup"><span data-stu-id="bc10d-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="bc10d-270">W dalszej części artykułu zobaczymy, jak można zmienić kolejność, a nawet dodać niestandardowego dostawcy kultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="bc10d-271">Jeśli żaden z dostawców można określić `DefaultRequestCulture` kultury żądania, jest używany.</span><span class="sxs-lookup"><span data-stu-id="bc10d-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="bc10d-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="bc10d-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="bc10d-273">Niektóre aplikacje będą używać ciągu zapytania, aby ustawić [kulturę i kulturę interfejsu użytkownika](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="bc10d-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="bc10d-274">W przypadku aplikacji, które używają podejścia nagłówka cookie lub Accept-Language dodawanie ciągu zapytania do adresu URL jest przydatne do debugowania i testowania kodu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="bc10d-275">Domyślnie `QueryStringRequestCultureProvider` jest zarejestrowany jako pierwszy dostawca lokalizacji `RequestCultureProvider` na liście.</span><span class="sxs-lookup"><span data-stu-id="bc10d-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="bc10d-276">Należy przekazać parametry `culture` ciągu `ui-culture`zapytania i .</span><span class="sxs-lookup"><span data-stu-id="bc10d-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="bc10d-277">Poniższy przykład ustawia określonej kultury (język i region) do hiszpański/Meksyk:</span><span class="sxs-lookup"><span data-stu-id="bc10d-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="bc10d-278">Jeśli przekażesz tylko w`culture` jednym `ui-culture`z dwóch ( lub ), dostawca ciągu zapytania ustawi obie wartości przy użyciu tej, która została przekazana.</span><span class="sxs-lookup"><span data-stu-id="bc10d-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="bc10d-279">Na przykład ustawienie tylko kultury ustawi `Culture` `UICulture`zarówno : i :</span><span class="sxs-lookup"><span data-stu-id="bc10d-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="bc10d-280">CookieRequestKulturaProvider</span><span class="sxs-lookup"><span data-stu-id="bc10d-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="bc10d-281">Aplikacje produkcyjne często zapewniają mechanizm, aby ustawić kulturę z ASP.NET core kultury cookie.</span><span class="sxs-lookup"><span data-stu-id="bc10d-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="bc10d-282">Użyj `MakeCookieValue` metody, aby utworzyć plik cookie.</span><span class="sxs-lookup"><span data-stu-id="bc10d-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="bc10d-283">Zwraca `CookieRequestCultureProvider` `DefaultCookieName` domyślną nazwę pliku cookie używaną do śledzenia preferowanych informacji o kulturze użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bc10d-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="bc10d-284">Domyślna nazwa `.AspNetCore.Culture`pliku cookie to .</span><span class="sxs-lookup"><span data-stu-id="bc10d-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="bc10d-285">Format pliku `c=%LANGCODE%|uic=%LANGCODE%`cookie `c` to `Culture` `uic` , `UICulture`gdzie jest i jest, na przykład:</span><span class="sxs-lookup"><span data-stu-id="bc10d-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="bc10d-286">Jeśli określisz tylko jedną z informacji o kulturze i kultury interfejsu użytkownika, określona kultura będzie używana zarówno dla informacji o kulturze, jak i kultury interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bc10d-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="bc10d-287">Nagłówek HTTP w języku akceptującym</span><span class="sxs-lookup"><span data-stu-id="bc10d-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="bc10d-288">[Nagłówek Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) jest ustawiany w większości przeglądarek i pierwotnie miał na celu określenie języka użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bc10d-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="bc10d-289">To ustawienie wskazuje, co przeglądarka została ustawiona do wysyłania lub została odziedziczona z podstawowego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="bc10d-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="bc10d-290">Nagłówek HTTP w języku akceptującym z żądania przeglądarki nie jest nieomylnym sposobem wykrywania preferowanego języka użytkownika (zobacz [Ustawianie preferencji językowych w przeglądarce).](https://www.w3.org/International/questions/qa-lang-priorities.en.php)</span><span class="sxs-lookup"><span data-stu-id="bc10d-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="bc10d-291">Aplikacja produkcyjna powinna zawierać sposób dla użytkownika, aby dostosować ich wybór kultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="bc10d-292">Ustawianie nagłówka HTTP w języku akceptującym w języku IE</span><span class="sxs-lookup"><span data-stu-id="bc10d-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="bc10d-293">W ikonie koła zębatego naciśnij pozycję **Opcje internetowe**.</span><span class="sxs-lookup"><span data-stu-id="bc10d-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="bc10d-294">Naciśnij pozycję **Języki**.</span><span class="sxs-lookup"><span data-stu-id="bc10d-294">Tap **Languages**.</span></span>

    ![Opcje internetowe](localization/_static/lang.png)

3. <span data-ttu-id="bc10d-296">Naciśnij pozycję **Ustaw preferencje językowe**.</span><span class="sxs-lookup"><span data-stu-id="bc10d-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="bc10d-297">Naciśnij pozycję **Dodaj język**.</span><span class="sxs-lookup"><span data-stu-id="bc10d-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="bc10d-298">Dodaj język.</span><span class="sxs-lookup"><span data-stu-id="bc10d-298">Add the language.</span></span>

6. <span data-ttu-id="bc10d-299">Naciśnij język, a następnie naciśnij pozycję **Przenieś w górę**.</span><span class="sxs-lookup"><span data-stu-id="bc10d-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="bc10d-300">Nagłówek HTTP w języku zawartości</span><span class="sxs-lookup"><span data-stu-id="bc10d-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="bc10d-301">Nagłówek encji [Język zawartości:](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language)</span><span class="sxs-lookup"><span data-stu-id="bc10d-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="bc10d-302">Służy do opisywania języka(-ów) przeznaczonego dla odbiorców.</span><span class="sxs-lookup"><span data-stu-id="bc10d-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="bc10d-303">Umożliwia użytkownikowi rozróżnianie według preferowanego języka użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bc10d-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="bc10d-304">Nagłówki encji są używane zarówno w żądaniach HTTP, jak i w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="bc10d-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="bc10d-305">Nagłówek `Content-Language` można dodać, ustawiając `ApplyCurrentCultureToResponseHeaders`właściwość .</span><span class="sxs-lookup"><span data-stu-id="bc10d-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="bc10d-306">Dodawanie `Content-Language` nagłówka:</span><span class="sxs-lookup"><span data-stu-id="bc10d-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="bc10d-307">Umożliwia requestlocalizationMiddleware ustawić `Content-Language` nagłówek z `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="bc10d-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="bc10d-308">Eliminuje konieczność jawnego ustawiania `Content-Language` nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="bc10d-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="bc10d-309">Korzystanie z niestandardowego dostawcy</span><span class="sxs-lookup"><span data-stu-id="bc10d-309">Use a custom provider</span></span>

<span data-ttu-id="bc10d-310">Załóżmy, że chcesz umożliwić klientom przechowywanie ich języka i kultury w bazach danych.</span><span class="sxs-lookup"><span data-stu-id="bc10d-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="bc10d-311">Można napisać dostawcę, aby wyszukać te wartości dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bc10d-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="bc10d-312">Poniższy kod pokazuje, jak dodać niestandardowego dostawcy:</span><span class="sxs-lookup"><span data-stu-id="bc10d-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

<span data-ttu-id="bc10d-313">Służy `RequestLocalizationOptions` do dodawania lub usuwania dostawców lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="bc10d-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="bc10d-314">Ustaw kulturę programowo</span><span class="sxs-lookup"><span data-stu-id="bc10d-314">Set the culture programmatically</span></span>

<span data-ttu-id="bc10d-315">Ten przykładowy projekt **Localization.StarterWeb** w [usłudze GitHub](https://github.com/aspnet/entropy) zawiera interfejs użytkownika, aby ustawić `Culture`plik .</span><span class="sxs-lookup"><span data-stu-id="bc10d-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="bc10d-316">*Plik Views/Shared/_SelectLanguagePartial.cshtml* umożliwia wybranie kultury z listy obsługiwanych kultur:</span><span class="sxs-lookup"><span data-stu-id="bc10d-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="bc10d-317">Plik *Views/Shared/_SelectLanguagePartial.cshtml* jest dodawany do `footer` sekcji pliku układu, dzięki czemu będzie dostępny dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="bc10d-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="bc10d-318">Metoda `SetLanguage` ustawia plik cookie kultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="bc10d-319">Nie można podłączyć *_SelectLanguagePartial.cshtml* do przykładowego kodu dla tego projektu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="bc10d-320">**Localization.StarterWeb** projektu w [usłudze GitHub](https://github.com/aspnet/entropy) ma kod do przepływu `RequestLocalizationOptions` do Razor częściowe za pośrednictwem [kontenera iniekcji zależności.](dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="bc10d-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="bc10d-321">Dane marszruty wiązania modelu i ciągi zapytań</span><span class="sxs-lookup"><span data-stu-id="bc10d-321">Model binding route data and query strings</span></span>

<span data-ttu-id="bc10d-322">Zobacz [Zachowanie globalizacji danych marszruty wiązania modelu i ciągów zapytań](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="bc10d-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="bc10d-323">Terminy globalizacji i lokalizacji</span><span class="sxs-lookup"><span data-stu-id="bc10d-323">Globalization and localization terms</span></span>

<span data-ttu-id="bc10d-324">Proces lokalizowania aplikacji wymaga również podstawowej wiedzy na temat odpowiednich zestawów znaków powszechnie używanych w nowoczesnym tworzeniu oprogramowania i zrozumienia problemów z nimi związanych.</span><span class="sxs-lookup"><span data-stu-id="bc10d-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="bc10d-325">Chociaż wszystkie komputery przechowują tekst jako liczby (kody), różne systemy przechowują ten sam tekst przy użyciu różnych liczb.</span><span class="sxs-lookup"><span data-stu-id="bc10d-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="bc10d-326">Proces lokalizacji odnosi się do tłumaczenia interfejsu użytkownika aplikacji (UI) dla określonej kultury/ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="bc10d-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="bc10d-327">[Lokalizacja](/dotnet/standard/globalization-localization/localizability-review) jest procesem pośrednim do sprawdzania, czy zglobalizowana aplikacja jest gotowa do lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="bc10d-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="bc10d-328">Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) dla nazwy `<languagecode2>-<country/regioncode2>`kultury `<languagecode2>` jest , `<country/regioncode2>` gdzie jest kod języka i jest kodem subkultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="bc10d-329">Na przykład `es-CL` dla języka `en-US` hiszpańskiego (Chile), `en-AU` angielskiego (Stany Zjednoczone) i angielskiego (Australia).</span><span class="sxs-lookup"><span data-stu-id="bc10d-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="bc10d-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) to połączenie dwuliterowego kodu kultury małych liter ISO 639 skojarzonego z językiem oraz dwuliterowego kodu subkultury ISO 3166 skojarzonego z krajem lub regionem.</span><span class="sxs-lookup"><span data-stu-id="bc10d-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="bc10d-331">Zobacz [Nazwa kultury językowej](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="bc10d-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="bc10d-332">Internacjonalizacja jest często skracana do "I18N".</span><span class="sxs-lookup"><span data-stu-id="bc10d-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="bc10d-333">Skrót przyjmuje pierwszą i ostatnią literę i liczbę liter między nimi, więc 18 oznacza liczbę liter między pierwszym "I" a ostatnim "N".</span><span class="sxs-lookup"><span data-stu-id="bc10d-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="bc10d-334">To samo dotyczy globalizacji (G11N) i lokalizacji (L10N).</span><span class="sxs-lookup"><span data-stu-id="bc10d-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="bc10d-335">Warunki:</span><span class="sxs-lookup"><span data-stu-id="bc10d-335">Terms:</span></span>

* <span data-ttu-id="bc10d-336">Globalizacja (G11N): proces tworzenia aplikacji obsługuje różne języki i regiony.</span><span class="sxs-lookup"><span data-stu-id="bc10d-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="bc10d-337">Lokalizacja (L10N): proces dostosowywania aplikacji dla danego języka i regionu.</span><span class="sxs-lookup"><span data-stu-id="bc10d-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="bc10d-338">Internacjonalizacja (I18N): Opisuje zarówno globalizację, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="bc10d-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="bc10d-339">Kultura: To język i, opcjonalnie, region.</span><span class="sxs-lookup"><span data-stu-id="bc10d-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="bc10d-340">Kultura neutralna: kultura, która ma określony język, ale nie region.</span><span class="sxs-lookup"><span data-stu-id="bc10d-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="bc10d-341">(na przykład "en", "es")</span><span class="sxs-lookup"><span data-stu-id="bc10d-341">(for example "en", "es")</span></span>
* <span data-ttu-id="bc10d-342">Określona kultura: Kultura, która ma określony język i region.</span><span class="sxs-lookup"><span data-stu-id="bc10d-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="bc10d-343">(na przykład "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="bc10d-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="bc10d-344">Kultura nadrzędna: kultura neutralna, która zawiera określoną kulturę.</span><span class="sxs-lookup"><span data-stu-id="bc10d-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="bc10d-345">(na przykład "en" jest kulturą nadrzędną "en-US" i "en-GB")</span><span class="sxs-lookup"><span data-stu-id="bc10d-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="bc10d-346">Ustawienia regionalne: Ustawienia regionalne są takie same jak kultury.</span><span class="sxs-lookup"><span data-stu-id="bc10d-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="bc10d-347">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bc10d-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="bc10d-348">[Localization.StarterWeb projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) używany w artykule.</span><span class="sxs-lookup"><span data-stu-id="bc10d-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="bc10d-349">Globalizacja i lokalizowanie aplikacji .NET</span><span class="sxs-lookup"><span data-stu-id="bc10d-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="bc10d-350">Zasoby w plikach resx</span><span class="sxs-lookup"><span data-stu-id="bc10d-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="bc10d-351">Zestaw narzędzi wielojęzycznych aplikacji firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="bc10d-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="bc10d-352">Lokalizacja & generycznych</span><span class="sxs-lookup"><span data-stu-id="bc10d-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)
