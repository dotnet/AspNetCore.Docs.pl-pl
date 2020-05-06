---
title: Globalizacja i lokalizacja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core udostępnia usługi i oprogramowanie pośredniczące do lokalizowania zawartości w różnych językach i kulturach.
ms.author: riande
ms.date: 11/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: 1111c0c57baf5bfd52b26b553179338100b9ba79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776568"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="4567f-103">Globalizacja i lokalizacja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4567f-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="4567f-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)i [Hisham bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="4567f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="4567f-105">Utworzenie wielojęzycznej witryny sieci Web z ASP.NET Core umożliwi witrynie dostęp do szerszego grona użytkowników.</span><span class="sxs-lookup"><span data-stu-id="4567f-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="4567f-106">ASP.NET Core udostępnia usługi i oprogramowanie pośredniczące do lokalizowania w różnych językach i kulturach.</span><span class="sxs-lookup"><span data-stu-id="4567f-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="4567f-107">Między innymi są używane [globalizacji](/dotnet/api/system.globalization) i [lokalizacje](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="4567f-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="4567f-108">Globalizacja to proces projektowania aplikacji, które obsługują różne kultury.</span><span class="sxs-lookup"><span data-stu-id="4567f-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="4567f-109">Globalizacja dodaje obsługę danych wejściowych, wyświetlanych i wyjściowych zdefiniowanego zestawu skryptów języka, które odnoszą się do określonych obszarów geograficznych.</span><span class="sxs-lookup"><span data-stu-id="4567f-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="4567f-110">Lokalizacja to proces adaptacji aplikacji, która została już przetworzona w celu zlokalizowania, do określonej kultury lub ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="4567f-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="4567f-111">Aby uzyskać więcej informacji **, zobacz sekcję globalizacja i warunki lokalizacji** na końcu tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4567f-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="4567f-112">Lokalizacja aplikacji obejmuje następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="4567f-112">App localization involves the following:</span></span>

1. <span data-ttu-id="4567f-113">Ustaw lokalizowalność zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="4567f-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="4567f-114">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="4567f-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="4567f-115">Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="4567f-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="4567f-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4567f-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="4567f-117">Ustaw lokalizowalność zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="4567f-117">Make the app's content localizable</span></span>

<span data-ttu-id="4567f-118">Wprowadzono w ASP.NET Core `IStringLocalizer` i `IStringLocalizer<T>` zostały zaprojektowane w celu zwiększenia produktywności podczas tworzenia zlokalizowanych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4567f-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="4567f-119">`IStringLocalizer`używa programu [ResourceManager](/dotnet/api/system.resources.resourcemanager) i [ResourceReader](/dotnet/api/system.resources.resourcereader) w celu zapewnienia zasobów specyficznych dla kultury w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="4567f-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="4567f-120">Prosty interfejs ma indeksator i `IEnumerable` zwraca zlokalizowane ciągi.</span><span class="sxs-lookup"><span data-stu-id="4567f-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="4567f-121">`IStringLocalizer`nie wymaga przechowywania w pliku zasobów domyślnych ciągów języka.</span><span class="sxs-lookup"><span data-stu-id="4567f-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="4567f-122">Możesz tworzyć aplikacje przeznaczone do lokalizacji i nie musisz już tworzyć plików zasobów w fazie opracowywania.</span><span class="sxs-lookup"><span data-stu-id="4567f-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="4567f-123">Poniższy kod przedstawia sposób zawijania ciągu "informacje o tytule" dla lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="4567f-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="4567f-124">W powyższym kodzie `IStringLocalizer<T>` implementacja pochodzi z [iniekcji zależności](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="4567f-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="4567f-125">Jeśli zlokalizowana wartość "informacje o tytule" nie zostanie znaleziona, zostanie zwrócony klucz indeksatora, czyli ciąg "informacje o tytule".</span><span class="sxs-lookup"><span data-stu-id="4567f-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="4567f-126">Możesz pozostawić domyślne ciągi literałów języka w aplikacji i otoczyć je w lokalizatorze, aby można było skupić się na tworzeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4567f-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="4567f-127">Tworzysz aplikację przy użyciu języka domyślnego i przygotujesz ją do kroku lokalizacji bez wcześniejszego tworzenia domyślnego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="4567f-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="4567f-128">Alternatywnie można użyć tradycyjnego podejścia i podać klucz do pobrania domyślnego ciągu języka.</span><span class="sxs-lookup"><span data-stu-id="4567f-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="4567f-129">Dla wielu deweloperów nowy przepływ pracy nie ma domyślnego pliku języka *. resx* i po prostu zawijający literały ciągu może zmniejszyć obciążenie lokalizowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4567f-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="4567f-130">Inni deweloperzy będą wolą tradycyjne przepływy pracy, ponieważ ułatwiają one pracę z dłuższymi literałami ciągów i ułatwiają aktualizowanie zlokalizowanych ciągów.</span><span class="sxs-lookup"><span data-stu-id="4567f-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="4567f-131">Użyj `IHtmlLocalizer<T>` implementacji dla zasobów, które zawierają kod HTML.</span><span class="sxs-lookup"><span data-stu-id="4567f-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="4567f-132">`IHtmlLocalizer`KOD HTML koduje argumenty, które są sformatowane w ciągu zasobu, ale nie kodu HTML samego samego ciągu zasobu.</span><span class="sxs-lookup"><span data-stu-id="4567f-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="4567f-133">W przykładzie wyróżnionym poniżej tylko wartość `name` parametru jest zakodowana w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="4567f-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="4567f-134">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.</span><span class="sxs-lookup"><span data-stu-id="4567f-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="4567f-135">Na najniższym poziomie można uzyskać `IStringLocalizerFactory` możliwość [iniekcji zależności](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="4567f-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="4567f-136">Powyższy kod demonstruje każdą z dwóch metod tworzenia fabryk.</span><span class="sxs-lookup"><span data-stu-id="4567f-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="4567f-137">Zlokalizowane ciągi można podzielić według kontrolera, obszaru lub tylko jednego kontenera.</span><span class="sxs-lookup"><span data-stu-id="4567f-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="4567f-138">W aplikacji przykładowej Klasa fikcyjna o nazwie `SharedResource` jest używana do współużytkowanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="4567f-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="4567f-139">Niektórzy Deweloperzy używają klasy `Startup` , aby zawierały ciągi globalne lub udostępnione.</span><span class="sxs-lookup"><span data-stu-id="4567f-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="4567f-140">W poniższym przykładzie są używane `InfoController` i `SharedResource` lokalizatory:</span><span class="sxs-lookup"><span data-stu-id="4567f-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="4567f-141">Wyświetl lokalizację</span><span class="sxs-lookup"><span data-stu-id="4567f-141">View localization</span></span>

<span data-ttu-id="4567f-142">`IViewLocalizer` Usługa udostępnia zlokalizowane ciągi dla [widoku](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="4567f-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="4567f-143">`ViewLocalizer` Klasa implementuje ten interfejs i odnajduje lokalizację zasobu ze ścieżki pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="4567f-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="4567f-144">Poniższy kod pokazuje, jak używać domyślnej implementacji `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="4567f-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="4567f-145">Domyślna implementacja programu `IViewLocalizer` znajduje plik zasobów na podstawie nazwy pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="4567f-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="4567f-146">Nie ma możliwości użycia globalnego pliku zasobów udostępnionych.</span><span class="sxs-lookup"><span data-stu-id="4567f-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="4567f-147">`ViewLocalizer`implementuje lokalizatora przy użyciu `IHtmlLocalizer`, dlatego nie można KODOWAĆ w formacie HTML zlokalizowanego ciągu.</span><span class="sxs-lookup"><span data-stu-id="4567f-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="4567f-148">Można Sparametryzuj ciągi zasobów i `IViewLocalizer` zakodować w kodzie HTML parametry, ale nie ciąg zasobu.</span><span class="sxs-lookup"><span data-stu-id="4567f-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="4567f-149">Weź pod uwagę następujące znaczniki Razor:</span><span class="sxs-lookup"><span data-stu-id="4567f-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="4567f-150">Plik zasobów francuski może zawierać następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="4567f-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="4567f-151">Klucz</span><span class="sxs-lookup"><span data-stu-id="4567f-151">Key</span></span> | <span data-ttu-id="4567f-152">Wartość</span><span class="sxs-lookup"><span data-stu-id="4567f-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="4567f-153">Renderowany widok będzie zawierać znacznik HTML z pliku zasobu.</span><span class="sxs-lookup"><span data-stu-id="4567f-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="4567f-154">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.</span><span class="sxs-lookup"><span data-stu-id="4567f-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="4567f-155">Aby użyć udostępnionego pliku zasobu w widoku, wstrzyknąć `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="4567f-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="4567f-156">Lokalizacja adnotacji</span><span class="sxs-lookup"><span data-stu-id="4567f-156">DataAnnotations localization</span></span>

<span data-ttu-id="4567f-157">Komunikaty o błędach DataAnnotations `IStringLocalizer<T>`są zlokalizowane przy użyciu.</span><span class="sxs-lookup"><span data-stu-id="4567f-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="4567f-158">Korzystając z opcji `ResourcesPath = "Resources"`, komunikaty o błędach `RegisterViewModel` w programie mogą być przechowywane w jednej z następujących ścieżek:</span><span class="sxs-lookup"><span data-stu-id="4567f-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="4567f-159">*Zasoby/modele widoków. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="4567f-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="4567f-160">*Zasoby/modele widoków/Account/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="4567f-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="4567f-161">W ASP.NET Core MVC 1.1.0 i wyższych atrybuty inne niż Walidacja są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="4567f-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="4567f-162">ASP.NET Core MVC 1,0 **nie** wyszukuje zlokalizowanych ciągów dla atrybutów niezwiązanych z walidacją.</span><span class="sxs-lookup"><span data-stu-id="4567f-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="4567f-163">Używanie jednego ciągu zasobu dla wielu klas</span><span class="sxs-lookup"><span data-stu-id="4567f-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="4567f-164">Poniższy kod przedstawia sposób użycia jednego ciągu zasobu do atrybutów walidacji z wieloma klasami:</span><span class="sxs-lookup"><span data-stu-id="4567f-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="4567f-165">W poprzednim kodzie, `SharedResource` jest klasą odpowiadającą resx, gdzie są przechowywane Twoje wiadomości weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="4567f-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="4567f-166">W tym podejściu DataAnnotations będą używać `SharedResource`tylko, a nie zasobów dla każdej klasy.</span><span class="sxs-lookup"><span data-stu-id="4567f-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="4567f-167">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="4567f-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="4567f-168">SupportedCultures i SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="4567f-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="4567f-169">ASP.NET Core pozwala określić dwie wartości kulturowe `SupportedCultures` i. `SupportedUICultures`</span><span class="sxs-lookup"><span data-stu-id="4567f-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="4567f-170">Obiekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) dla `SupportedCultures` określa wyniki funkcji zależnych od kultury, takich jak data, godzina, liczba i formatowanie waluty.</span><span class="sxs-lookup"><span data-stu-id="4567f-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="4567f-171">`SupportedCultures`określa również kolejność sortowania tekstu, Konwencji wielkości liter i porównań ciągów.</span><span class="sxs-lookup"><span data-stu-id="4567f-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="4567f-172">Zobacz [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) , aby uzyskać więcej informacji na temat sposobu, w jaki serwer pobiera kulturę.</span><span class="sxs-lookup"><span data-stu-id="4567f-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="4567f-173">Określa, które przetłumaczone ciągi (z plików *resx* ) są wyszukiwane przez program [ResourceManager.](/dotnet/api/system.resources.resourcemanager) `SupportedUICultures`</span><span class="sxs-lookup"><span data-stu-id="4567f-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="4567f-174">`ResourceManager` Po prostu wyszukuje ciągi specyficzne dla kultury, które są określane `CurrentUICulture`przez.</span><span class="sxs-lookup"><span data-stu-id="4567f-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="4567f-175">Każdy wątek w programie .NET `CurrentCulture` ma `CurrentUICulture` obiekty i.</span><span class="sxs-lookup"><span data-stu-id="4567f-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="4567f-176">ASP.NET Core sprawdza te wartości podczas renderowania funkcji zależnych od kultury.</span><span class="sxs-lookup"><span data-stu-id="4567f-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="4567f-177">Na przykład, jeśli kultura bieżącego wątku jest ustawiona na wartość "en-US" (angielski, Stany Zjednoczone), `DateTime.Now.ToLongDateString()` zostanie wyświetlona wartość "czwartek, 18 lutego, 2016" `CurrentCulture` , ale jeśli jest ustawiona na "es-ES" (hiszpański, Hiszpania) dane wyjściowe będą "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="4567f-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="4567f-178">Pliki zasobów</span><span class="sxs-lookup"><span data-stu-id="4567f-178">Resource files</span></span>

<span data-ttu-id="4567f-179">Plik zasobów jest użytecznym mechanizmem oddzielania lokalizowalnych ciągów od kodu.</span><span class="sxs-lookup"><span data-stu-id="4567f-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="4567f-180">Przetłumaczone ciągi dla języka innego niż domyślny są izolowane w plikach zasobów *resx* .</span><span class="sxs-lookup"><span data-stu-id="4567f-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="4567f-181">Na przykład możesz chcieć utworzyć plik zasobów hiszpański o nazwie *Welcome. es. resx* zawierający przetłumaczone ciągi.</span><span class="sxs-lookup"><span data-stu-id="4567f-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="4567f-182">"es" to kod języka w języku hiszpańskim.</span><span class="sxs-lookup"><span data-stu-id="4567f-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="4567f-183">Aby utworzyć ten plik zasobów w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="4567f-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="4567f-184">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder, który będzie zawierać plik zasobów, > **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="4567f-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Zagnieżdżone menu kontekstowe: w Eksplorator rozwiązań menu kontekstowe jest otwarte dla zasobów.](localization/_static/newi.png)

2. <span data-ttu-id="4567f-187">W polu **wyszukiwania zainstalowanych szablonów** wprowadź "Resource" i Nazwij plik.</span><span class="sxs-lookup"><span data-stu-id="4567f-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Okno dialogowe Dodawanie nowego elementu](localization/_static/res.png)

3. <span data-ttu-id="4567f-189">Wprowadź wartość klucza (ciąg macierzysty) w kolumnie **Nazwa** i przetłumaczony ciąg w kolumnie **wartość** .</span><span class="sxs-lookup"><span data-stu-id="4567f-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome. es. resx plik (plik zasobów powitalnych dla języka hiszpańskiego) z słowem Hello w kolumnie Nazwa i słowem Hola (Hello w języku hiszpańskim) w kolumnie wartość](localization/_static/hola.png)

    <span data-ttu-id="4567f-191">Program Visual Studio wyświetla plik *Welcome. es. resx* .</span><span class="sxs-lookup"><span data-stu-id="4567f-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Eksplorator rozwiązań przedstawiający plik zasobów programu Welcome hiszpański (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="4567f-193">Nazewnictwo plików zasobów</span><span class="sxs-lookup"><span data-stu-id="4567f-193">Resource file naming</span></span>

<span data-ttu-id="4567f-194">Zasoby są nazwane dla pełnej nazwy typu swojej klasy pomniejszonej o nazwę zestawu.</span><span class="sxs-lookup"><span data-stu-id="4567f-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="4567f-195">Na przykład zasób francuski w projekcie, którego głównym zestawem jest `LocalizationWebsite.Web.dll` dla klasy `LocalizationWebsite.Web.Startup` , będzie nazwany *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="4567f-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="4567f-196">Zasób klasy `LocalizationWebsite.Web.Controllers.HomeController` nosi nazwę *controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="4567f-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="4567f-197">Jeśli przestrzeń nazw klasy Target nie jest taka sama jak nazwa zestawu, będzie potrzebna pełna nazwa typu.</span><span class="sxs-lookup"><span data-stu-id="4567f-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="4567f-198">Przykładowo w przykładowym projekcie zasób dla typu `ExtraNamespace.Tools` powinien mieć nazwę *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="4567f-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="4567f-199">W przykładowym projekcie `ConfigureServices` Metoda ustawia `ResourcesPath` do "zasobów", więc ścieżka względna projektu dla francuskiego pliku zasobów kontrolera głównego to *zasoby/kontrolery. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="4567f-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="4567f-200">Alternatywnie można użyć folderów do organizowania plików zasobów.</span><span class="sxs-lookup"><span data-stu-id="4567f-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="4567f-201">W przypadku kontrolera macierzystego ścieżka będzie zawierać *zasoby/kontrolery/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="4567f-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="4567f-202">Jeśli ta `ResourcesPath` opcja nie zostanie użyta, plik *. resx* zostanie użyty w katalogu bazowym projektu.</span><span class="sxs-lookup"><span data-stu-id="4567f-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="4567f-203">Plik zasobu dla `HomeController` nosi nazwę *controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="4567f-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="4567f-204">Wybór przy użyciu konwencji nazewnictwa kropka lub ścieżki zależy od tego, w jaki sposób chcesz zorganizować pliki zasobów.</span><span class="sxs-lookup"><span data-stu-id="4567f-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="4567f-205">Nazwa zasobu</span><span class="sxs-lookup"><span data-stu-id="4567f-205">Resource name</span></span> | <span data-ttu-id="4567f-206">Nazwa kropka lub ścieżki</span><span class="sxs-lookup"><span data-stu-id="4567f-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="4567f-207">Zasoby/kontrolery. HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="4567f-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="4567f-208">Kropka</span><span class="sxs-lookup"><span data-stu-id="4567f-208">Dot</span></span>  |
| <span data-ttu-id="4567f-209">Zasoby/kontrolery/HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="4567f-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="4567f-210">Ścieżka</span><span class="sxs-lookup"><span data-stu-id="4567f-210">Path</span></span> |
|    |     |

<span data-ttu-id="4567f-211">Pliki zasobów `@inject IViewLocalizer` używające Razor w widokach są zgodne z podobnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="4567f-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="4567f-212">Plik zasobów dla widoku może być nazwany przy użyciu nazw kropek lub nazw ścieżek.</span><span class="sxs-lookup"><span data-stu-id="4567f-212">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="4567f-213">Wyświetl pliki zasobów, aby naśladować ścieżkę skojarzonego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="4567f-213"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="4567f-214">Przy założeniu, `ResourcesPath` że ustawimy na "zasoby", plik zasobów francuski skojarzony z widokiem */Home/about. cshtml* może mieć jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="4567f-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="4567f-215">Zasoby/widoki/Strona główna/informacje. fr. resx</span><span class="sxs-lookup"><span data-stu-id="4567f-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="4567f-216">Zasoby/widoki. Strona główna. informacje. fr. resx</span><span class="sxs-lookup"><span data-stu-id="4567f-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="4567f-217">Jeśli `ResourcesPath` opcja nie zostanie użyta, plik *resx* dla widoku powinien znajdować się w tym samym folderze co widok.</span><span class="sxs-lookup"><span data-stu-id="4567f-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="4567f-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="4567f-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="4567f-219">Atrybut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) udostępnia główną przestrzeń nazw zestawu, gdy główna przestrzeń nazw zestawu jest inna niż nazwa zestawu.</span><span class="sxs-lookup"><span data-stu-id="4567f-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="4567f-220">Taka sytuacja może wystąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="4567f-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="4567f-221">Na przykład `my-project-name.csproj` użyje głównej przestrzeni nazw `my_project_name` i nazwy `my-project-name` zestawu prowadzącej do tego błędu.</span><span class="sxs-lookup"><span data-stu-id="4567f-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="4567f-222">Jeśli główna przestrzeń nazw zestawu jest inna niż nazwa zestawu:</span><span class="sxs-lookup"><span data-stu-id="4567f-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="4567f-223">Lokalizacja nie działa domyślnie.</span><span class="sxs-lookup"><span data-stu-id="4567f-223">Localization does not work by default.</span></span>
* <span data-ttu-id="4567f-224">Lokalizowanie nie powiedzie się z powodu sposobu wyszukiwania zasobów w zestawie.</span><span class="sxs-lookup"><span data-stu-id="4567f-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="4567f-225">`RootNamespace`jest wartością czasu kompilacji, która nie jest dostępna dla wykonywanego procesu.</span><span class="sxs-lookup"><span data-stu-id="4567f-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="4567f-226">Jeśli różni `RootNamespace` się od `AssemblyName`, należy uwzględnić następujące w *AssemblyInfo.cs* (z wartościami parametrów zamienionymi na wartości rzeczywiste):</span><span class="sxs-lookup"><span data-stu-id="4567f-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="4567f-227">Poprzedni kod umożliwia pomyślne rozpoznanie plików resx.</span><span class="sxs-lookup"><span data-stu-id="4567f-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="4567f-228">Zachowanie rezerwowe kultury</span><span class="sxs-lookup"><span data-stu-id="4567f-228">Culture fallback behavior</span></span>

<span data-ttu-id="4567f-229">Podczas wyszukiwania zasobu lokalizacja prowadzi do "rezerwy kulturowej".</span><span class="sxs-lookup"><span data-stu-id="4567f-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="4567f-230">Jeśli nie zostanie znaleziona, rozpoczyna się od żądanej kultury, przywraca kulturę nadrzędną tej kultury.</span><span class="sxs-lookup"><span data-stu-id="4567f-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="4567f-231">Poza tym Właściwość [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) reprezentuje kulturę nadrzędną.</span><span class="sxs-lookup"><span data-stu-id="4567f-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="4567f-232">Zwykle (ale nie zawsze) oznacza usunięcie z ISO.</span><span class="sxs-lookup"><span data-stu-id="4567f-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="4567f-233">Na przykład dialekt języka hiszpańskiego wymawianego w Meksyku to "es-MX".</span><span class="sxs-lookup"><span data-stu-id="4567f-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="4567f-234">Jest to wersja Hiszpańska "es"&mdash;, która nie jest specyficzna dla żadnego z krajów.</span><span class="sxs-lookup"><span data-stu-id="4567f-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="4567f-235">Wyobraź sobie, że witryna otrzymuje żądanie dotyczące zasobu "Welcome" przy użyciu kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="4567f-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="4567f-236">System lokalizacji wyszukuje następujące zasoby w kolejności i wybiera pierwsze dopasowanie:</span><span class="sxs-lookup"><span data-stu-id="4567f-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="4567f-237">*Welcome.fr — CA. resx*</span><span class="sxs-lookup"><span data-stu-id="4567f-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="4567f-238">*Witamy. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="4567f-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="4567f-239">*Welcome. resx* (Jeśli `NeutralResourcesLanguage` jest "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="4567f-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="4567f-240">Na przykład, jeśli usuniesz oznaczenie kultury ". fr" i masz kulturę ustawioną na francuski, domyślny plik zasobów jest odczytywany, a ciągi są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="4567f-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="4567f-241">Menedżer zasobów określa domyślny lub rezerwowy zasób, gdy nic nie spełnia wymaganej kultury.</span><span class="sxs-lookup"><span data-stu-id="4567f-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="4567f-242">Jeśli chcesz po prostu zwrócić klucz, gdy brakuje zasobu dla wymaganej kultury, nie musisz mieć domyślnego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="4567f-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="4567f-243">Generowanie plików zasobów przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4567f-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="4567f-244">Jeśli utworzysz plik zasobów w programie Visual Studio bez kultury w nazwie pliku (na przykład *Welcome. resx*), program Visual Studio utworzy klasę języka C# z właściwością dla każdego ciągu.</span><span class="sxs-lookup"><span data-stu-id="4567f-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="4567f-245">Zwykle nie jest to możliwe dzięki ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4567f-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="4567f-246">Zazwyczaj nie istnieje domyślny plik zasobów *resx* (plik *. resx* bez nazwy kultury).</span><span class="sxs-lookup"><span data-stu-id="4567f-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="4567f-247">Zalecamy utworzenie pliku *resx* z nazwą kultury (na przykład *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="4567f-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="4567f-248">Podczas tworzenia pliku *resx* przy użyciu nazwy kultury program Visual Studio nie generuje pliku klasy.</span><span class="sxs-lookup"><span data-stu-id="4567f-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="4567f-249">Dodaj inne kultury</span><span class="sxs-lookup"><span data-stu-id="4567f-249">Add other cultures</span></span>

<span data-ttu-id="4567f-250">Każda kombinacja języka i kultury (oprócz języka domyślnego) wymaga unikatowego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="4567f-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="4567f-251">Tworzysz pliki zasobów dla różnych kultur i ustawień regionalnych, tworząc nowe pliki zasobów, w których kody języka ISO są częścią nazwy pliku (na przykład **en-us**, **fr-CA**i **pl-GB**).</span><span class="sxs-lookup"><span data-stu-id="4567f-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="4567f-252">Te kody ISO są umieszczane między nazwami plików i rozszerzeniem *resx* , jak w *Welcome.es-MX. resx* (hiszpański/Meksyk).</span><span class="sxs-lookup"><span data-stu-id="4567f-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="4567f-253">Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="4567f-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="4567f-254">Konfigurowanie lokalizacji</span><span class="sxs-lookup"><span data-stu-id="4567f-254">Configure localization</span></span>

<span data-ttu-id="4567f-255">Lokalizacja jest skonfigurowana w ramach `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="4567f-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="4567f-256">`AddLocalization`Dodaje usługi lokalizacyjne do kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="4567f-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="4567f-257">Powyższy kod również ustawia ścieżkę zasobów na "zasoby".</span><span class="sxs-lookup"><span data-stu-id="4567f-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="4567f-258">`AddViewLocalization`Dodaje obsługę zlokalizowanych plików widoku.</span><span class="sxs-lookup"><span data-stu-id="4567f-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="4567f-259">Ta lokalizacja widoku przykładowego jest oparta na sufiksie pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="4567f-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="4567f-260">Na przykład "fr" w pliku *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4567f-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="4567f-261">`AddDataAnnotationsLocalization`Dodaje obsługę zlokalizowanych `DataAnnotations` komunikatów weryfikacyjnych `IStringLocalizer` za pomocą abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="4567f-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="4567f-262">Oprogramowanie pośredniczące lokalizacji</span><span class="sxs-lookup"><span data-stu-id="4567f-262">Localization middleware</span></span>

<span data-ttu-id="4567f-263">Bieżąca kultura w żądaniu jest ustawiana w oprogramowaniu [pośredniczącym](xref:fundamentals/middleware/index)lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="4567f-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="4567f-264">Oprogramowanie pośredniczące lokalizacji jest włączone w tej `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="4567f-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="4567f-265">Oprogramowanie pośredniczące lokalizacyjne musi być skonfigurowane przed jakimkolwiek oprogramowanie pośredniczące, które może sprawdzić kulturę żądania ( `app.UseMvcWithDefaultRoute()`na przykład).</span><span class="sxs-lookup"><span data-stu-id="4567f-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="4567f-266">`UseRequestLocalization`Inicjuje `RequestLocalizationOptions` obiekt.</span><span class="sxs-lookup"><span data-stu-id="4567f-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="4567f-267">Na każdym zażądaniu listy `RequestCultureProvider` w programie `RequestLocalizationOptions` jest wyliczany, a pierwszy dostawca, który może pomyślnie ustalić kulturę żądań, jest używany.</span><span class="sxs-lookup"><span data-stu-id="4567f-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="4567f-268">Dostawcy domyślnie pochodzą z `RequestLocalizationOptions` klasy:</span><span class="sxs-lookup"><span data-stu-id="4567f-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="4567f-269">Lista domyślna przechodzi od najbardziej konkretnych do najmniej określonych.</span><span class="sxs-lookup"><span data-stu-id="4567f-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="4567f-270">W dalszej części artykułu zobaczymy, jak można zmienić kolejność, a nawet dodać niestandardowego dostawcę kultury.</span><span class="sxs-lookup"><span data-stu-id="4567f-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="4567f-271">Jeśli żaden z dostawców nie może określić kultury żądania, `DefaultRequestCulture` jest używana.</span><span class="sxs-lookup"><span data-stu-id="4567f-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="4567f-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="4567f-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="4567f-273">Niektóre aplikacje będą używać ciągu zapytania w celu ustawienia kultur [i kultury interfejsu użytkownika](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="4567f-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="4567f-274">W przypadku aplikacji korzystających z metody pliku cookie lub z nagłówka Accept-Language Dodawanie ciągu zapytania do adresu URL jest przydatne w przypadku debugowania i testowania kodu.</span><span class="sxs-lookup"><span data-stu-id="4567f-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="4567f-275">Domyślnie program `QueryStringRequestCultureProvider` jest rejestrowany jako pierwszy dostawca lokalizacji na `RequestCultureProvider` liście.</span><span class="sxs-lookup"><span data-stu-id="4567f-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="4567f-276">Parametry `culture` ciągu zapytania są przekazywane i `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="4567f-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="4567f-277">Poniższy przykład ustawia określoną kulturę (język i region) na hiszpański/Meksyk:</span><span class="sxs-lookup"><span data-stu-id="4567f-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="4567f-278">Jeśli przekazujesz tylko jeden z dwóch (`culture` lub `ui-culture`), dostawca ciągu zapytania ustawi obie wartości przy użyciu przekazanego elementu.</span><span class="sxs-lookup"><span data-stu-id="4567f-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="4567f-279">Na przykład ustawienie tylko kulturowe ustawi zarówno, `Culture` jak i: `UICulture`</span><span class="sxs-lookup"><span data-stu-id="4567f-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="4567f-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="4567f-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="4567f-281">Aplikacje produkcyjne często udostępniają mechanizm ustawiania kultury przy użyciu pliku cookie ASP.NET Core kultury.</span><span class="sxs-lookup"><span data-stu-id="4567f-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="4567f-282">Użyj metody `MakeCookieValue` , aby utworzyć plik cookie.</span><span class="sxs-lookup"><span data-stu-id="4567f-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="4567f-283">`CookieRequestCultureProvider` Zwraca domyślną nazwę pliku cookie używaną do śledzenia preferowanych informacji o kulturze `DefaultCookieName` użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4567f-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="4567f-284">Domyślna nazwa pliku cookie to `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="4567f-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="4567f-285">Format pliku cookie to `c=%LANGCODE%|uic=%LANGCODE%`, gdzie `c` is `Culture` i `uic` is `UICulture`, na przykład:</span><span class="sxs-lookup"><span data-stu-id="4567f-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="4567f-286">Jeśli określisz tylko jedną z informacji o kulturze i kulturze interfejsu użytkownika, określona kultura zostanie użyta dla informacji kultury i kultury interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4567f-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="4567f-287">Nagłówek Accept-Language HTTP</span><span class="sxs-lookup"><span data-stu-id="4567f-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="4567f-288">[Nagłówek Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) jest settable w większości przeglądarek i był pierwotnie przeznaczony do określenia języka użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4567f-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="4567f-289">To ustawienie wskazuje, co przeglądarka została ustawiona do wysłania lub która dziedziczy z bazowego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="4567f-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="4567f-290">Nagłówek Accept-Language HTTP z żądania przeglądarki nie jest infallibleym sposobem wykrywania preferowanego języka użytkownika (zobacz [Ustawianie preferencji językowych w przeglądarce](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="4567f-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="4567f-291">Aplikacja produkcyjna powinna uwzględniać sposób, w jaki użytkownik może dostosować wybór kultury.</span><span class="sxs-lookup"><span data-stu-id="4567f-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="4567f-292">Ustawianie nagłówka HTTP Accept-Language w programie IE</span><span class="sxs-lookup"><span data-stu-id="4567f-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="4567f-293">Na ikonie koła zębatego naciśnij pozycję **Opcje internetowe**.</span><span class="sxs-lookup"><span data-stu-id="4567f-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="4567f-294">Naciśnij pozycję **Języki**.</span><span class="sxs-lookup"><span data-stu-id="4567f-294">Tap **Languages**.</span></span>

    ![Opcje internetowe](localization/_static/lang.png)

3. <span data-ttu-id="4567f-296">Naciśnij pozycję **Ustaw preferencje językowe**.</span><span class="sxs-lookup"><span data-stu-id="4567f-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="4567f-297">Naciśnij pozycję **Dodaj język**.</span><span class="sxs-lookup"><span data-stu-id="4567f-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="4567f-298">Dodaj język.</span><span class="sxs-lookup"><span data-stu-id="4567f-298">Add the language.</span></span>

6. <span data-ttu-id="4567f-299">Naciśnij pozycję język, a następnie naciśnij pozycję **Przenieś w górę**.</span><span class="sxs-lookup"><span data-stu-id="4567f-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="4567f-300">Nagłówek HTTP w języku zawartości</span><span class="sxs-lookup"><span data-stu-id="4567f-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="4567f-301">Nagłówek jednostki [zawartości w języku](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="4567f-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="4567f-302">Służy do opisywania języków przeznaczonych dla odbiorców.</span><span class="sxs-lookup"><span data-stu-id="4567f-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="4567f-303">Umożliwia użytkownikowi odróżnienie zgodnie z preferowanym językiem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4567f-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="4567f-304">Nagłówki jednostek są używane w żądaniach i odpowiedziach HTTP.</span><span class="sxs-lookup"><span data-stu-id="4567f-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="4567f-305">`Content-Language` Nagłówek można dodać przez ustawienie właściwości `ApplyCurrentCultureToResponseHeaders`.</span><span class="sxs-lookup"><span data-stu-id="4567f-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="4567f-306">Dodawanie `Content-Language` nagłówka:</span><span class="sxs-lookup"><span data-stu-id="4567f-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="4567f-307">Zezwala RequestLocalizationMiddleware na ustawienie `Content-Language` nagłówka przy użyciu. `CurrentUICulture`</span><span class="sxs-lookup"><span data-stu-id="4567f-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="4567f-308">Eliminuje konieczność jawnego ustawiania nagłówka `Content-Language` odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="4567f-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="4567f-309">Używanie dostawcy niestandardowego</span><span class="sxs-lookup"><span data-stu-id="4567f-309">Use a custom provider</span></span>

<span data-ttu-id="4567f-310">Załóżmy, że chcesz zezwolić klientom na przechowywanie w swoich bazach danych językowych i kulturowych.</span><span class="sxs-lookup"><span data-stu-id="4567f-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="4567f-311">Można napisać dostawcę, aby wyszukać te wartości dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4567f-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="4567f-312">Poniższy kod pokazuje, jak dodać niestandardowego dostawcę:</span><span class="sxs-lookup"><span data-stu-id="4567f-312">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="4567f-313">Służy `RequestLocalizationOptions` do dodawania lub usuwania dostawców lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="4567f-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="4567f-314">Ustaw kulturę programowo</span><span class="sxs-lookup"><span data-stu-id="4567f-314">Set the culture programmatically</span></span>

<span data-ttu-id="4567f-315">Ten przykład **lokalizacji. StarterWeb** projekt w witrynie [GitHub](https://github.com/aspnet/entropy) zawiera interfejs użytkownika służący do ustawiania `Culture`.</span><span class="sxs-lookup"><span data-stu-id="4567f-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="4567f-316">Plik *views/Shared/_SelectLanguagePartial. cshtml* umożliwia wybranie kultury z listy obsługiwanych kultur:</span><span class="sxs-lookup"><span data-stu-id="4567f-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="4567f-317">Plik *views/Shared/_SelectLanguagePartial. cshtml* zostanie dodany do `footer` sekcji pliku układu, więc będzie dostępny dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="4567f-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="4567f-318">`SetLanguage` Metoda ustawia plik cookie kultury.</span><span class="sxs-lookup"><span data-stu-id="4567f-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="4567f-319">Nie można podłączyć *_SelectLanguagePartial. cshtml* do przykładowego kodu dla tego projektu.</span><span class="sxs-lookup"><span data-stu-id="4567f-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="4567f-320">Projekt **Lokalizacja. StarterWeb** w usłudze [GitHub](https://github.com/aspnet/entropy) ma kod, `RequestLocalizationOptions` który umożliwia przechodzenie Razor do częściowej przez kontener [iniekcji zależności](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="4567f-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="4567f-321">Dane tras powiązań modelu i ciągi zapytania</span><span class="sxs-lookup"><span data-stu-id="4567f-321">Model binding route data and query strings</span></span>

<span data-ttu-id="4567f-322">Zobacz [zachowanie globalizacji powiązań modelu powiązanie danych i ciągi zapytań](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="4567f-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="4567f-323">Warunki globalizacji i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="4567f-323">Globalization and localization terms</span></span>

<span data-ttu-id="4567f-324">Proces lokalizowania aplikacji wymaga również podstawowej znajomości odpowiednich zestawów znaków, które są często używane w nowoczesnych opracowywaniu oprogramowania i zrozumieniu skojarzonych z nimi problemów.</span><span class="sxs-lookup"><span data-stu-id="4567f-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="4567f-325">Mimo że wszystkie komputery przechowują tekst jako cyfry (kody), różne systemy przechowują ten sam tekst przy użyciu różnych liczb.</span><span class="sxs-lookup"><span data-stu-id="4567f-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="4567f-326">Proces lokalizowania dotyczy tłumaczenia interfejsu użytkownika aplikacji (UI) dla określonych kultur/ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="4567f-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="4567f-327">Możliwość [lokalizowania](/dotnet/standard/globalization-localization/localizability-review) to proces pośredni służący do sprawdzania, czy aplikacja globalna jest gotowa do lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="4567f-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="4567f-328">Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) dla nazwy kultury to `<languagecode2>-<country/regioncode2>`, gdzie `<languagecode2>` jest kodem języka i `<country/regioncode2>` jest kodem podkultury.</span><span class="sxs-lookup"><span data-stu-id="4567f-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="4567f-329">Na przykład `es-CL` w przypadku języka hiszpańskiego (Chile `en-US` ) dla języka angielskiego (Stany Zjednoczone) `en-AU` i dla języka angielskiego (Australia).</span><span class="sxs-lookup"><span data-stu-id="4567f-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="4567f-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) jest kombinacją kodu ISO 639 2 litery małymi literami związanymi z językiem i ISO 3166 2 literą w postaci wielkiej litery, skojarzonej z krajem lub regionem.</span><span class="sxs-lookup"><span data-stu-id="4567f-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="4567f-331">Zobacz [nazwa kultury języka](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="4567f-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="4567f-332">Międzynarodowe jest często skracane do "I18N".</span><span class="sxs-lookup"><span data-stu-id="4567f-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="4567f-333">Skrót przyjmuje pierwszą i ostatnią literę oraz liczbę liter między nimi, więc 18 oznacza liczbę liter między pierwszym "I" i ostatnim "N".</span><span class="sxs-lookup"><span data-stu-id="4567f-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="4567f-334">Dotyczy to zarówno globalizacji (G11N), jak i lokalizacji (L10N).</span><span class="sxs-lookup"><span data-stu-id="4567f-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="4567f-335">Odsetk</span><span class="sxs-lookup"><span data-stu-id="4567f-335">Terms:</span></span>

* <span data-ttu-id="4567f-336">Globalizacja (G11N): proces tworzenia aplikacji w różnych językach i regionach.</span><span class="sxs-lookup"><span data-stu-id="4567f-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="4567f-337">Lokalizacja (L10N): proces dostosowywania aplikacji dla danego języka i regionu.</span><span class="sxs-lookup"><span data-stu-id="4567f-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="4567f-338">Międzynarodowe (I18N): opisuje zarówno globalizację, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="4567f-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="4567f-339">Kultura: jest to język i, opcjonalnie, region.</span><span class="sxs-lookup"><span data-stu-id="4567f-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="4567f-340">Kultura neutralna: kultura, która ma określony język, ale nie region.</span><span class="sxs-lookup"><span data-stu-id="4567f-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="4567f-341">(na przykład "en", "es")</span><span class="sxs-lookup"><span data-stu-id="4567f-341">(for example "en", "es")</span></span>
* <span data-ttu-id="4567f-342">Określona kultura: kultura, która ma określony język i region.</span><span class="sxs-lookup"><span data-stu-id="4567f-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="4567f-343">(na przykład "en-US", "pl-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="4567f-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="4567f-344">Kultura nadrzędna: neutralna kultura, która zawiera określoną kulturę.</span><span class="sxs-lookup"><span data-stu-id="4567f-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="4567f-345">(na przykład "en" jest kulturą nadrzędną wartości "pl-US" i "pl-GB")</span><span class="sxs-lookup"><span data-stu-id="4567f-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="4567f-346">Ustawienia regionalne: ustawienie regionalne jest takie samo jak kultura.</span><span class="sxs-lookup"><span data-stu-id="4567f-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4567f-347">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4567f-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="4567f-348">[Projekt lokalizacji. StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) używany w artykule.</span><span class="sxs-lookup"><span data-stu-id="4567f-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="4567f-349">Globalizacja i lokalizowanie aplikacji platformy .NET</span><span class="sxs-lookup"><span data-stu-id="4567f-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="4567f-350">Zasoby w plikach resx</span><span class="sxs-lookup"><span data-stu-id="4567f-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="4567f-351">Zestaw Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="4567f-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="4567f-352">Lokalizacje & typy ogólne</span><span class="sxs-lookup"><span data-stu-id="4567f-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)
