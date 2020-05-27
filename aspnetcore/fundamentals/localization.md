---
<span data-ttu-id="e5c7b-101">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-102">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-103">'Identity'</span></span>
- <span data-ttu-id="e5c7b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-105">'Razor'</span></span>
- <span data-ttu-id="e5c7b-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="e5c7b-107">Globalizacja i lokalizacja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5c7b-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="e5c7b-108">[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)i [Hisham bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="e5c7b-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="e5c7b-109">Witryna internetowa w wielu językach umożliwia dostęp do szerszego grona użytkowników.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="e5c7b-110">ASP.NET Core udostępnia usługi i oprogramowanie pośredniczące do lokalizowania w różnych językach i kulturach.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="e5c7b-111">Między innymi są używane [globalizacji](/dotnet/api/system.globalization) i [lokalizacje](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="e5c7b-112">Globalizacja to proces projektowania aplikacji, które obsługują różne kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="e5c7b-113">Globalizacja dodaje obsługę danych wejściowych, wyświetlanych i wyjściowych zdefiniowanego zestawu skryptów języka, które odnoszą się do określonych obszarów geograficznych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="e5c7b-114">Lokalizacja to proces adaptacji aplikacji, która została już przetworzona w celu zlokalizowania, do określonej kultury lub ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="e5c7b-115">Aby uzyskać więcej informacji **, zobacz sekcję globalizacja i warunki lokalizacji** na końcu tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="e5c7b-116">Lokalizacja aplikacji obejmuje następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-116">App localization involves the following:</span></span>

1. <span data-ttu-id="e5c7b-117">Ustaw lokalizowalność zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="e5c7b-118">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="e5c7b-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="e5c7b-119">Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="e5c7b-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="e5c7b-120">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e5c7b-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="e5c7b-121">Ustaw lokalizowalność zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-121">Make the app's content localizable</span></span>

<span data-ttu-id="e5c7b-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' nie wymaga zapisywania w pliku zasobów ciągów języka domyślnego.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="e5c7b-123">Możesz tworzyć aplikacje przeznaczone do lokalizacji i nie musisz już tworzyć plików zasobów w fazie opracowywania.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="e5c7b-124">Poniższy kod przedstawia sposób zawijania ciągu "informacje o tytule" dla lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="e5c7b-125">W poprzednim kodzie `IStringLocalizer<T>` implementacja pochodzi z [iniekcji zależności](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="e5c7b-126">Jeśli zlokalizowana wartość "informacje o tytule" nie zostanie znaleziona, zostanie zwrócony klucz indeksatora, czyli ciąg "informacje o tytule".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="e5c7b-127">Możesz pozostawić domyślne ciągi literałów języka w aplikacji i otoczyć je w lokalizatorze, aby można było skupić się na tworzeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="e5c7b-128">Tworzysz aplikację przy użyciu języka domyślnego i przygotujesz ją do kroku lokalizacji bez wcześniejszego tworzenia domyślnego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="e5c7b-129">Alternatywnie można użyć tradycyjnego podejścia i podać klucz do pobrania domyślnego ciągu języka.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="e5c7b-130">Dla wielu deweloperów nowy przepływ pracy nie ma domyślnego pliku języka *. resx* i po prostu zawijający literały ciągu może zmniejszyć obciążenie lokalizowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="e5c7b-131">Inni deweloperzy będą wolą tradycyjne przepływy pracy, ponieważ ułatwiają one pracę z dłuższymi literałami ciągów i ułatwiają aktualizowanie zlokalizowanych ciągów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="e5c7b-132">Użyj `IHtmlLocalizer<T>` implementacji dla zasobów, które zawierają kod HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="e5c7b-133">`IHtmlLocalizer`KOD HTML koduje argumenty, które są sformatowane w ciągu zasobu, ale nie kodu HTML samego samego ciągu zasobu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="e5c7b-134">W przykładzie wyróżnionym poniżej tylko wartość `name` parametru jest zakodowana w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="e5c7b-135">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="e5c7b-136">Na najniższym poziomie można uzyskać możliwość `IStringLocalizerFactory` [iniekcji zależności](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="e5c7b-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="e5c7b-137">Powyższy kod demonstruje każdą z dwóch metod tworzenia fabryk.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="e5c7b-138">Zlokalizowane ciągi można podzielić według kontrolera, obszaru lub tylko jednego kontenera.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="e5c7b-139">W aplikacji przykładowej Klasa fikcyjna o nazwie `SharedResource` jest używana do współużytkowanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="e5c7b-140">Niektórzy Deweloperzy używają `Startup` klasy, aby zawierały ciągi globalne lub udostępnione.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="e5c7b-141">W poniższym przykładzie `InfoController` `SharedResource` są używane i lokalizatory:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="e5c7b-142">Wyświetl lokalizację</span><span class="sxs-lookup"><span data-stu-id="e5c7b-142">View localization</span></span>

<span data-ttu-id="e5c7b-143">`IViewLocalizer`Usługa udostępnia zlokalizowane ciągi dla [widoku](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="e5c7b-144">`ViewLocalizer`Klasa implementuje ten interfejs i odnajduje lokalizację zasobu ze ścieżki pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="e5c7b-145">Poniższy kod pokazuje, jak używać domyślnej implementacji `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="e5c7b-146">Domyślna implementacja programu `IViewLocalizer` znajduje plik zasobów na podstawie nazwy pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="e5c7b-147">Nie ma możliwości użycia globalnego pliku zasobów udostępnionych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="e5c7b-148">`ViewLocalizer`implementuje lokalizatora przy użyciu `IHtmlLocalizer` , dlatego Razor nie KODUJ kodu HTML zlokalizowanego ciągu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="e5c7b-149">Można Sparametryzuj ciągi zasobów i `IViewLocalizer` zakodować w kodzie HTML parametry, ale nie ciąg zasobu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="e5c7b-150">Rozważ następujące Razor oznakowanie:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="e5c7b-151">Plik zasobów francuski może zawierać następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="e5c7b-152">Klucz</span><span class="sxs-lookup"><span data-stu-id="e5c7b-152">Key</span></span> | <span data-ttu-id="e5c7b-153">Wartość</span><span class="sxs-lookup"><span data-stu-id="e5c7b-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="e5c7b-154">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-155">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-156">'Identity'</span></span>
- <span data-ttu-id="e5c7b-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-158">'Razor'</span></span>
- <span data-ttu-id="e5c7b-159">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-159">'SignalR' uid:</span></span> 

<span data-ttu-id="e5c7b-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="e5c7b-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="e5c7b-161">Renderowany widok będzie zawierać znacznik HTML z pliku zasobu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="e5c7b-162">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="e5c7b-163">Aby użyć udostępnionego pliku zasobu w widoku, wstrzyknąć `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="e5c7b-164">Lokalizacja adnotacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-164">DataAnnotations localization</span></span>

<span data-ttu-id="e5c7b-165">Komunikaty o błędach DataAnnotations są zlokalizowane przy użyciu `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="e5c7b-166">Korzystając z opcji `ResourcesPath = "Resources"` , komunikaty o błędach w programie `RegisterViewModel` mogą być przechowywane w jednej z następujących ścieżek:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="e5c7b-167">*Zasoby/modele widoków. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="e5c7b-168">*Zasoby/modele widoków/Account/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="e5c7b-169">W ASP.NET Core MVC 1.1.0 i wyższych atrybuty inne niż Walidacja są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="e5c7b-170">ASP.NET Core MVC 1,0 **nie** wyszukuje zlokalizowanych ciągów dla atrybutów niezwiązanych z walidacją.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="e5c7b-171">Używanie jednego ciągu zasobu dla wielu klas</span><span class="sxs-lookup"><span data-stu-id="e5c7b-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="e5c7b-172">Poniższy kod przedstawia sposób użycia jednego ciągu zasobu do atrybutów walidacji z wieloma klasami:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="e5c7b-173">W poprzednim kodzie, `SharedResource` jest klasą odpowiadającą resx, gdzie są przechowywane Twoje wiadomości weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="e5c7b-174">W tym podejściu DataAnnotations będą używać tylko `SharedResource` , a nie zasobów dla każdej klasy.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="e5c7b-175">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="e5c7b-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="e5c7b-176">SupportedCultures i SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="e5c7b-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="e5c7b-177">ASP.NET Core pozwala określić dwie wartości kulturowe `SupportedCultures` i `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="e5c7b-178">Obiekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) dla `SupportedCultures` określa wyniki funkcji zależnych od kultury, takich jak data, godzina, liczba i formatowanie waluty.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="e5c7b-179">`SupportedCultures`określa również kolejność sortowania tekstu, Konwencji wielkości liter i porównań ciągów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="e5c7b-180">Zobacz [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) , aby uzyskać więcej informacji na temat sposobu, w jaki serwer pobiera kulturę.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="e5c7b-181">`SupportedUICultures`Określa, które przetłumaczone ciągi (z plików *resx* ) są wyszukiwane przez program [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="e5c7b-182">`ResourceManager`Po prostu wyszukuje ciągi specyficzne dla kultury, które są określane przez `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="e5c7b-183">Każdy wątek w programie .NET `CurrentCulture` ma `CurrentUICulture` obiekty i.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="e5c7b-184">ASP.NET Core sprawdza te wartości podczas renderowania funkcji zależnych od kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="e5c7b-185">Na przykład, jeśli kultura bieżącego wątku jest ustawiona na wartość "en-US" (angielski, Stany Zjednoczone), `DateTime.Now.ToLongDateString()` zostanie wyświetlona wartość "czwartek, 18 lutego, 2016", ale jeśli `CurrentCulture` jest ustawiona na "ES-es" (hiszpański, Hiszpania) dane wyjściowe będą "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="e5c7b-186">Pliki zasobów</span><span class="sxs-lookup"><span data-stu-id="e5c7b-186">Resource files</span></span>

<span data-ttu-id="e5c7b-187">Plik zasobów jest użytecznym mechanizmem oddzielania lokalizowalnych ciągów od kodu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="e5c7b-188">Przetłumaczone ciągi dla języka innego niż domyślny są izolowane w plikach zasobów *resx* .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="e5c7b-189">Na przykład możesz chcieć utworzyć plik zasobów hiszpański o nazwie *Welcome. es. resx* zawierający przetłumaczone ciągi.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="e5c7b-190">"es" to kod języka w języku hiszpańskim.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="e5c7b-191">Aby utworzyć ten plik zasobów w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="e5c7b-192">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder, który będzie zawierać plik zasobów, > **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Zagnieżdżone menu kontekstowe: w Eksplorator rozwiązań menu kontekstowe jest otwarte dla zasobów.](localization/_static/newi.png)

2. <span data-ttu-id="e5c7b-195">W polu **wyszukiwania zainstalowanych szablonów** wprowadź "Resource" i Nazwij plik.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Okno dialogowe Dodawanie nowego elementu](localization/_static/res.png)

3. <span data-ttu-id="e5c7b-197">Wprowadź wartość klucza (ciąg macierzysty) w kolumnie **Nazwa** i przetłumaczony ciąg w kolumnie **wartość** .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome. es. resx plik (plik zasobów powitalnych dla języka hiszpańskiego) z słowem Hello w kolumnie Nazwa i słowem Hola (Hello w języku hiszpańskim) w kolumnie wartość](localization/_static/hola.png)

    <span data-ttu-id="e5c7b-199">Program Visual Studio wyświetla plik *Welcome. es. resx* .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Eksplorator rozwiązań przedstawiający plik zasobów programu Welcome hiszpański (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="e5c7b-201">Nazewnictwo plików zasobów</span><span class="sxs-lookup"><span data-stu-id="e5c7b-201">Resource file naming</span></span>

<span data-ttu-id="e5c7b-202">Zasoby są nazwane dla pełnej nazwy typu swojej klasy pomniejszonej o nazwę zestawu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="e5c7b-203">Na przykład zasób francuski w projekcie, którego głównym zestawem jest `LocalizationWebsite.Web.dll` dla klasy, będzie `LocalizationWebsite.Web.Startup` nazwany *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="e5c7b-204">Zasób klasy `LocalizationWebsite.Web.Controllers.HomeController` nosi nazwę *controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-205">Jeśli przestrzeń nazw klasy Target nie jest taka sama jak nazwa zestawu, będzie potrzebna pełna nazwa typu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="e5c7b-206">Przykładowo w przykładowym projekcie zasób dla typu `ExtraNamespace.Tools` powinien mieć nazwę *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="e5c7b-207">W przykładowym projekcie `ConfigureServices` Metoda ustawia `ResourcesPath` do "zasobów", więc ścieżka względna projektu dla francuskiego pliku zasobów kontrolera głównego to *zasoby/kontrolery. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-208">Alternatywnie można użyć folderów do organizowania plików zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="e5c7b-209">W przypadku kontrolera macierzystego ścieżka będzie zawierać *zasoby/kontrolery/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-210">Jeśli ta opcja nie zostanie użyta `ResourcesPath` , plik *. resx* zostanie użyty w katalogu bazowym projektu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="e5c7b-211">Plik zasobu dla `HomeController` nosi nazwę *controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-212">Wybór przy użyciu konwencji nazewnictwa kropka lub ścieżki zależy od tego, w jaki sposób chcesz zorganizować pliki zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="e5c7b-213">Nazwa zasobu</span><span class="sxs-lookup"><span data-stu-id="e5c7b-213">Resource name</span></span> | <span data-ttu-id="e5c7b-214">Nazwa kropka lub ścieżki</span><span class="sxs-lookup"><span data-stu-id="e5c7b-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="e5c7b-215">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-216">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-217">'Identity'</span></span>
- <span data-ttu-id="e5c7b-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-219">'Razor'</span></span>
- <span data-ttu-id="e5c7b-220">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-221">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-222">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-223">'Identity'</span></span>
- <span data-ttu-id="e5c7b-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-225">'Razor'</span></span>
- <span data-ttu-id="e5c7b-226">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-227">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-228">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-229">'Identity'</span></span>
- <span data-ttu-id="e5c7b-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-231">'Razor'</span></span>
- <span data-ttu-id="e5c7b-232">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-233">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-234">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-235">'Identity'</span></span>
- <span data-ttu-id="e5c7b-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-237">'Razor'</span></span>
- <span data-ttu-id="e5c7b-238">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-238">'SignalR' uid:</span></span> 

<span data-ttu-id="e5c7b-239">------   | ---title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-240">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-241">'Identity'</span></span>
- <span data-ttu-id="e5c7b-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-243">'Razor'</span></span>
- <span data-ttu-id="e5c7b-244">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-245">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-246">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-247">'Identity'</span></span>
- <span data-ttu-id="e5c7b-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-249">'Razor'</span></span>
- <span data-ttu-id="e5c7b-250">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-251">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-252">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-253">'Identity'</span></span>
- <span data-ttu-id="e5c7b-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-255">'Razor'</span></span>
- <span data-ttu-id="e5c7b-256">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-257">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-258">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-259">'Identity'</span></span>
- <span data-ttu-id="e5c7b-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-261">'Razor'</span></span>
- <span data-ttu-id="e5c7b-262">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-262">'SignalR' uid:</span></span> 

<span data-ttu-id="e5c7b-263">------- | | Zasoby/kontrolery. HomeController. fr. resx | Kropka | | Zasoby/kontrolery/HomeController. fr. resx | Ścieżka | |    |     |</span><span class="sxs-lookup"><span data-stu-id="e5c7b-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="e5c7b-264">Pliki zasobów używające `@inject IViewLocalizer` w Razor widokach są zgodne z podobnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="e5c7b-265">Plik zasobów dla widoku może być nazwany przy użyciu nazw kropek lub nazw ścieżek.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-265">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="e5c7b-266">Wyświetl pliki zasobów, aby naśladować ścieżkę skojarzonego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-266"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="e5c7b-267">Przy założeniu, że ustawimy `ResourcesPath` na "zasoby", plik zasobów francuski skojarzony z widokiem */Home/about. cshtml* może mieć jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="e5c7b-268">Zasoby/widoki/Strona główna/informacje. fr. resx</span><span class="sxs-lookup"><span data-stu-id="e5c7b-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="e5c7b-269">Zasoby/widoki. Strona główna. informacje. fr. resx</span><span class="sxs-lookup"><span data-stu-id="e5c7b-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="e5c7b-270">Jeśli opcja nie zostanie użyta `ResourcesPath` , plik *resx* dla widoku powinien znajdować się w tym samym folderze co widok.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="e5c7b-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="e5c7b-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="e5c7b-272">Atrybut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) udostępnia główną przestrzeń nazw zestawu, gdy główna przestrzeń nazw zestawu jest inna niż nazwa zestawu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="e5c7b-273">Taka sytuacja może wystąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="e5c7b-274">Na przykład `my-project-name.csproj` użyje głównej przestrzeni nazw `my_project_name` i nazwy zestawu `my-project-name` prowadzącej do tego błędu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="e5c7b-275">Jeśli główna przestrzeń nazw zestawu jest inna niż nazwa zestawu:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="e5c7b-276">Lokalizacja nie działa domyślnie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-276">Localization does not work by default.</span></span>
* <span data-ttu-id="e5c7b-277">Lokalizowanie nie powiedzie się z powodu sposobu wyszukiwania zasobów w zestawie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="e5c7b-278">`RootNamespace`jest wartością czasu kompilacji, która nie jest dostępna dla wykonywanego procesu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="e5c7b-279">Jeśli `RootNamespace` różni się od `AssemblyName` , należy uwzględnić następujące w *AssemblyInfo.cs* (z wartościami parametrów zamienionymi na wartości rzeczywiste):</span><span class="sxs-lookup"><span data-stu-id="e5c7b-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="e5c7b-280">Poprzedni kod umożliwia pomyślne rozpoznanie plików resx.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="e5c7b-281">Zachowanie rezerwowe kultury</span><span class="sxs-lookup"><span data-stu-id="e5c7b-281">Culture fallback behavior</span></span>

<span data-ttu-id="e5c7b-282">Podczas wyszukiwania zasobu lokalizacja prowadzi do "rezerwy kulturowej".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="e5c7b-283">Jeśli nie zostanie znaleziona, rozpoczyna się od żądanej kultury, przywraca kulturę nadrzędną tej kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="e5c7b-284">Poza tym Właściwość [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) reprezentuje kulturę nadrzędną.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="e5c7b-285">Zwykle (ale nie zawsze) oznacza usunięcie z ISO.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="e5c7b-286">Na przykład dialekt języka hiszpańskiego wymawianego w Meksyku to "es-MX".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="e5c7b-287">Jest to wersja Hiszpańska "es", która &mdash; nie jest specyficzna dla żadnego z krajów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="e5c7b-288">Wyobraź sobie, że witryna otrzymuje żądanie dotyczące zasobu "Welcome" przy użyciu kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="e5c7b-289">System lokalizacji wyszukuje następujące zasoby w kolejności i wybiera pierwsze dopasowanie:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="e5c7b-290">*Welcome.fr — CA. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="e5c7b-291">*Witamy. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="e5c7b-292">*Welcome. resx* (Jeśli `NeutralResourcesLanguage` jest "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="e5c7b-293">Na przykład, jeśli usuniesz oznaczenie kultury ". fr" i masz kulturę ustawioną na francuski, domyślny plik zasobów jest odczytywany, a ciągi są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="e5c7b-294">Menedżer zasobów określa domyślny lub rezerwowy zasób, gdy nic nie spełnia wymaganej kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="e5c7b-295">Jeśli chcesz po prostu zwrócić klucz, gdy brakuje zasobu dla wymaganej kultury, nie musisz mieć domyślnego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="e5c7b-296">Generowanie plików zasobów przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5c7b-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="e5c7b-297">Jeśli utworzysz plik zasobów w programie Visual Studio bez kultury w nazwie pliku (na przykład *Welcome. resx*), program Visual Studio utworzy klasę języka C# z właściwością dla każdego ciągu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="e5c7b-298">Zwykle nie jest to możliwe dzięki ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="e5c7b-299">Zazwyczaj nie istnieje domyślny plik zasobów *resx* (plik *. resx* bez nazwy kultury).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="e5c7b-300">Zalecamy utworzenie pliku *resx* z nazwą kultury (na przykład *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="e5c7b-301">Podczas tworzenia pliku *resx* przy użyciu nazwy kultury program Visual Studio nie generuje pliku klasy.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="e5c7b-302">Dodaj inne kultury</span><span class="sxs-lookup"><span data-stu-id="e5c7b-302">Add other cultures</span></span>

<span data-ttu-id="e5c7b-303">Każda kombinacja języka i kultury (oprócz języka domyślnego) wymaga unikatowego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="e5c7b-304">Tworzysz pliki zasobów dla różnych kultur i ustawień regionalnych, tworząc nowe pliki zasobów, w których kody języka ISO są częścią nazwy pliku (na przykład **en-us**, **fr-CA**i **pl-GB**).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="e5c7b-305">Te kody ISO są umieszczane między nazwami plików i rozszerzeniem *resx* , jak w *Welcome.es-MX. resx* (hiszpański/Meksyk).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="e5c7b-306">Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="e5c7b-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="e5c7b-307">Konfigurowanie lokalizacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-307">Configure localization</span></span>

<span data-ttu-id="e5c7b-308">Lokalizacja jest skonfigurowana w ramach `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="e5c7b-309">`AddLocalization`Dodaje usługi lokalizacyjne do kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="e5c7b-310">Powyższy kod również ustawia ścieżkę zasobów na "zasoby".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="e5c7b-311">`AddViewLocalization`Dodaje obsługę zlokalizowanych plików widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="e5c7b-312">Ta lokalizacja widoku przykładowego jest oparta na sufiksie pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="e5c7b-313">Na przykład "fr" w pliku *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="e5c7b-314">`AddDataAnnotationsLocalization`Dodaje obsługę zlokalizowanych `DataAnnotations` komunikatów weryfikacyjnych za pomocą `IStringLocalizer` abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="e5c7b-315">Oprogramowanie pośredniczące lokalizacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-315">Localization middleware</span></span>

<span data-ttu-id="e5c7b-316">Bieżąca kultura w żądaniu jest ustawiana w oprogramowaniu [pośredniczącym](xref:fundamentals/middleware/index)lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="e5c7b-317">Oprogramowanie pośredniczące lokalizacji jest włączone w tej `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="e5c7b-318">Oprogramowanie pośredniczące lokalizacyjne musi być skonfigurowane przed jakimkolwiek oprogramowanie pośredniczące, które może sprawdzić kulturę żądania (na przykład `app.UseMvcWithDefaultRoute()` ).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e5c7b-319">`UseRequestLocalization`Inicjuje `RequestLocalizationOptions` obiekt.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="e5c7b-320">Na każdym zażądaniu listy `RequestCultureProvider` w programie `RequestLocalizationOptions` jest wyliczany, a pierwszy dostawca, który może pomyślnie ustalić kulturę żądań, jest używany.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="e5c7b-321">Dostawcy domyślnie pochodzą z `RequestLocalizationOptions` klasy:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="e5c7b-322">Lista domyślna przechodzi od najbardziej konkretnych do najmniej określonych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="e5c7b-323">W dalszej części artykułu zobaczymy, jak można zmienić kolejność, a nawet dodać niestandardowego dostawcę kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="e5c7b-324">Jeśli żaden z dostawców nie może określić kultury żądania, `DefaultRequestCulture` jest używana.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="e5c7b-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e5c7b-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="e5c7b-326">Niektóre aplikacje będą używać ciągu zapytania w celu ustawienia kultur [i kultury interfejsu użytkownika](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="e5c7b-327">W przypadku aplikacji korzystających z metody pliku cookie lub z nagłówka Accept-Language Dodawanie ciągu zapytania do adresu URL jest przydatne w przypadku debugowania i testowania kodu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="e5c7b-328">Domyślnie program `QueryStringRequestCultureProvider` jest rejestrowany jako pierwszy dostawca lokalizacji na `RequestCultureProvider` liście.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="e5c7b-329">Parametry ciągu zapytania są przekazywane `culture` i `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="e5c7b-330">Poniższy przykład ustawia określoną kulturę (język i region) na hiszpański/Meksyk:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="e5c7b-331">Jeśli przekazujesz tylko jeden z dwóch ( `culture` lub `ui-culture` ), dostawca ciągu zapytania ustawi obie wartości przy użyciu przekazanego elementu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="e5c7b-332">Na przykład ustawienie tylko kulturowe ustawi zarówno, `Culture` jak i `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="e5c7b-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e5c7b-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="e5c7b-334">Aplikacje produkcyjne często udostępniają mechanizm ustawiania kultury przy użyciu pliku cookie ASP.NET Core kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="e5c7b-335">Użyj `MakeCookieValue` metody, aby utworzyć plik cookie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="e5c7b-336">`CookieRequestCultureProvider` `DefaultCookieName` Zwraca domyślną nazwę pliku cookie używaną do śledzenia preferowanych informacji o kulturze użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="e5c7b-337">Domyślna nazwa pliku cookie to `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="e5c7b-338">Format pliku cookie to `c=%LANGCODE%|uic=%LANGCODE%` , gdzie `c` is `Culture` i `uic` is `UICulture` , na przykład:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="e5c7b-339">Jeśli określisz tylko jedną z informacji o kulturze i kulturze interfejsu użytkownika, określona kultura zostanie użyta dla informacji kultury i kultury interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="e5c7b-340">Nagłówek Accept-Language HTTP</span><span class="sxs-lookup"><span data-stu-id="e5c7b-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="e5c7b-341">[Nagłówek Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) jest settable w większości przeglądarek i był pierwotnie przeznaczony do określenia języka użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="e5c7b-342">To ustawienie wskazuje, co przeglądarka została ustawiona do wysłania lub która dziedziczy z bazowego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="e5c7b-343">Nagłówek Accept-Language HTTP z żądania przeglądarki nie jest infallibleym sposobem wykrywania preferowanego języka użytkownika (zobacz [Ustawianie preferencji językowych w przeglądarce](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="e5c7b-344">Aplikacja produkcyjna powinna uwzględniać sposób, w jaki użytkownik może dostosować wybór kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="e5c7b-345">Ustawianie nagłówka HTTP Accept-Language w programie IE</span><span class="sxs-lookup"><span data-stu-id="e5c7b-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="e5c7b-346">Na ikonie koła zębatego naciśnij pozycję **Opcje internetowe**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="e5c7b-347">Naciśnij pozycję **Języki**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-347">Tap **Languages**.</span></span>

    ![Opcje internetowe](localization/_static/lang.png)

3. <span data-ttu-id="e5c7b-349">Naciśnij pozycję **Ustaw preferencje językowe**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="e5c7b-350">Naciśnij pozycję **Dodaj język**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="e5c7b-351">Dodaj język.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-351">Add the language.</span></span>

6. <span data-ttu-id="e5c7b-352">Naciśnij pozycję język, a następnie naciśnij pozycję **Przenieś w górę**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="e5c7b-353">Używanie dostawcy niestandardowego</span><span class="sxs-lookup"><span data-stu-id="e5c7b-353">Use a custom provider</span></span>

<span data-ttu-id="e5c7b-354">Załóżmy, że chcesz zezwolić klientom na przechowywanie w swoich bazach danych językowych i kulturowych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="e5c7b-355">Można napisać dostawcę, aby wyszukać te wartości dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="e5c7b-356">Poniższy kod pokazuje, jak dodać niestandardowego dostawcę:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-356">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="e5c7b-357">Służy `RequestLocalizationOptions` do dodawania lub usuwania dostawców lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="e5c7b-358">Ustaw kulturę programowo</span><span class="sxs-lookup"><span data-stu-id="e5c7b-358">Set the culture programmatically</span></span>

<span data-ttu-id="e5c7b-359">Ten przykład **lokalizacji. StarterWeb** projekt w witrynie [GitHub](https://github.com/aspnet/entropy) zawiera interfejs użytkownika służący do ustawiania `Culture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="e5c7b-360">Plik *views/Shared/_SelectLanguagePartial. cshtml* umożliwia wybranie kultury z listy obsługiwanych kultur:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="e5c7b-361">Plik *views/Shared/_SelectLanguagePartial. cshtml* zostanie dodany do `footer` sekcji pliku układu, więc będzie dostępny dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="e5c7b-362">`SetLanguage`Metoda ustawia plik cookie kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="e5c7b-363">Nie można podłączyć *_SelectLanguagePartial. cshtml* do przykładowego kodu dla tego projektu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="e5c7b-364">Projekt **Lokalizacja. StarterWeb** w usłudze [GitHub](https://github.com/aspnet/entropy) ma kod, który umożliwia `RequestLocalizationOptions` Razor Przechodzenie do częściowej przez kontener [iniekcji zależności](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="e5c7b-365">Dane tras powiązań modelu i ciągi zapytania</span><span class="sxs-lookup"><span data-stu-id="e5c7b-365">Model binding route data and query strings</span></span>

<span data-ttu-id="e5c7b-366">Zobacz [zachowanie globalizacji powiązań modelu powiązanie danych i ciągi zapytań](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="e5c7b-367">Warunki globalizacji i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="e5c7b-367">Globalization and localization terms</span></span>

<span data-ttu-id="e5c7b-368">Proces lokalizowania aplikacji wymaga również podstawowej znajomości odpowiednich zestawów znaków, które są często używane w nowoczesnych opracowywaniu oprogramowania i zrozumieniu skojarzonych z nimi problemów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="e5c7b-369">Mimo że wszystkie komputery przechowują tekst jako cyfry (kody), różne systemy przechowują ten sam tekst przy użyciu różnych liczb.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="e5c7b-370">Proces lokalizowania dotyczy tłumaczenia interfejsu użytkownika aplikacji (UI) dla określonych kultur/ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="e5c7b-371">Możliwość [lokalizowania](/dotnet/standard/globalization-localization/localizability-review) to proces pośredni służący do sprawdzania, czy aplikacja globalna jest gotowa do lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="e5c7b-372">Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) dla nazwy kultury to `<languagecode2>-<country/regioncode2>` , gdzie `<languagecode2>` jest kodem języka i `<country/regioncode2>` jest kodem podkultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="e5c7b-373">Na przykład `es-CL` w przypadku języka hiszpańskiego (Chile) `en-US` dla języka angielskiego (Stany Zjednoczone) i `en-AU` dla języka angielskiego (Australia).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="e5c7b-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) jest kombinacją kodu ISO 639 2 litery małymi literami związanymi z językiem i ISO 3166 2 literą w postaci wielkiej litery, skojarzonej z krajem lub regionem.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="e5c7b-375">Zobacz [nazwa kultury języka](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="e5c7b-376">Międzynarodowe jest często skracane do "I18N".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="e5c7b-377">Skrót przyjmuje pierwszą i ostatnią literę oraz liczbę liter między nimi, więc 18 oznacza liczbę liter między pierwszym "I" i ostatnim "N".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="e5c7b-378">Dotyczy to zarówno globalizacji (G11N), jak i lokalizacji (L10N).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="e5c7b-379">Odsetk</span><span class="sxs-lookup"><span data-stu-id="e5c7b-379">Terms:</span></span>

* <span data-ttu-id="e5c7b-380">Globalizacja (G11N): proces tworzenia aplikacji w różnych językach i regionach.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="e5c7b-381">Lokalizacja (L10N): proces dostosowywania aplikacji dla danego języka i regionu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="e5c7b-382">Międzynarodowe (I18N): opisuje zarówno globalizację, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="e5c7b-383">Kultura: jest to język i, opcjonalnie, region.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="e5c7b-384">Kultura neutralna: kultura, która ma określony język, ale nie region.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="e5c7b-385">(na przykład "en", "es")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-385">(for example "en", "es")</span></span>
* <span data-ttu-id="e5c7b-386">Określona kultura: kultura, która ma określony język i region.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="e5c7b-387">(na przykład "en-US", "pl-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="e5c7b-388">Kultura nadrzędna: neutralna kultura, która zawiera określoną kulturę.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="e5c7b-389">(na przykład "en" jest kulturą nadrzędną wartości "pl-US" i "pl-GB")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="e5c7b-390">Ustawienia regionalne: ustawienie regionalne jest takie samo jak kultura.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="e5c7b-391">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="e5c7b-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="e5c7b-392">[Projekt lokalizacji. StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) używany w artykule.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="e5c7b-393">Globalizacja i lokalizowanie aplikacji platformy .NET</span><span class="sxs-lookup"><span data-stu-id="e5c7b-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="e5c7b-394">Zasoby w plikach resx</span><span class="sxs-lookup"><span data-stu-id="e5c7b-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="e5c7b-395">Zestaw Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="e5c7b-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="e5c7b-396">Lokalizacje & typy ogólne</span><span class="sxs-lookup"><span data-stu-id="e5c7b-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e5c7b-397">[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)i [Hisham bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="e5c7b-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="e5c7b-398">Witryna internetowa w wielu językach umożliwia dostęp do szerszego grona użytkowników.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="e5c7b-399">ASP.NET Core udostępnia usługi i oprogramowanie pośredniczące do lokalizowania w różnych językach i kulturach.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="e5c7b-400">Między innymi są używane [globalizacji](/dotnet/api/system.globalization) i [lokalizacje](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="e5c7b-401">Globalizacja to proces projektowania aplikacji, które obsługują różne kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="e5c7b-402">Globalizacja dodaje obsługę danych wejściowych, wyświetlanych i wyjściowych zdefiniowanego zestawu skryptów języka, które odnoszą się do określonych obszarów geograficznych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="e5c7b-403">Lokalizacja to proces adaptacji aplikacji, która została już przetworzona w celu zlokalizowania, do określonej kultury lub ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="e5c7b-404">Aby uzyskać więcej informacji **, zobacz sekcję globalizacja i warunki lokalizacji** na końcu tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="e5c7b-405">Lokalizacja aplikacji obejmuje następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-405">App localization involves the following:</span></span>

1. <span data-ttu-id="e5c7b-406">Ustaw lokalizowalność zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="e5c7b-407">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="e5c7b-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="e5c7b-408">Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="e5c7b-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="e5c7b-409">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e5c7b-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="e5c7b-410">Ustaw lokalizowalność zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-410">Make the app's content localizable</span></span>

<span data-ttu-id="e5c7b-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' nie wymaga zapisywania w pliku zasobów ciągów języka domyślnego.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="e5c7b-412">Możesz tworzyć aplikacje przeznaczone do lokalizacji i nie musisz już tworzyć plików zasobów w fazie opracowywania.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="e5c7b-413">Poniższy kod przedstawia sposób zawijania ciągu "informacje o tytule" dla lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="e5c7b-414">W poprzednim kodzie `IStringLocalizer<T>` implementacja pochodzi z [iniekcji zależności](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="e5c7b-415">Jeśli zlokalizowana wartość "informacje o tytule" nie zostanie znaleziona, zostanie zwrócony klucz indeksatora, czyli ciąg "informacje o tytule".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="e5c7b-416">Możesz pozostawić domyślne ciągi literałów języka w aplikacji i otoczyć je w lokalizatorze, aby można było skupić się na tworzeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="e5c7b-417">Tworzysz aplikację przy użyciu języka domyślnego i przygotujesz ją do kroku lokalizacji bez wcześniejszego tworzenia domyślnego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="e5c7b-418">Alternatywnie można użyć tradycyjnego podejścia i podać klucz do pobrania domyślnego ciągu języka.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="e5c7b-419">Dla wielu deweloperów nowy przepływ pracy nie ma domyślnego pliku języka *. resx* i po prostu zawijający literały ciągu może zmniejszyć obciążenie lokalizowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="e5c7b-420">Inni deweloperzy będą wolą tradycyjne przepływy pracy, ponieważ ułatwiają one pracę z dłuższymi literałami ciągów i ułatwiają aktualizowanie zlokalizowanych ciągów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="e5c7b-421">Użyj `IHtmlLocalizer<T>` implementacji dla zasobów, które zawierają kod HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="e5c7b-422">`IHtmlLocalizer`KOD HTML koduje argumenty, które są sformatowane w ciągu zasobu, ale nie kodu HTML samego samego ciągu zasobu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="e5c7b-423">W przykładzie wyróżnionym poniżej tylko wartość `name` parametru jest zakodowana w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="e5c7b-424">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="e5c7b-425">Na najniższym poziomie można uzyskać możliwość `IStringLocalizerFactory` [iniekcji zależności](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="e5c7b-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="e5c7b-426">Powyższy kod demonstruje każdą z dwóch metod tworzenia fabryk.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="e5c7b-427">Zlokalizowane ciągi można podzielić według kontrolera, obszaru lub tylko jednego kontenera.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="e5c7b-428">W aplikacji przykładowej Klasa fikcyjna o nazwie `SharedResource` jest używana do współużytkowanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="e5c7b-429">Niektórzy Deweloperzy używają `Startup` klasy, aby zawierały ciągi globalne lub udostępnione.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="e5c7b-430">W poniższym przykładzie `InfoController` `SharedResource` są używane i lokalizatory:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="e5c7b-431">Wyświetl lokalizację</span><span class="sxs-lookup"><span data-stu-id="e5c7b-431">View localization</span></span>

<span data-ttu-id="e5c7b-432">`IViewLocalizer`Usługa udostępnia zlokalizowane ciągi dla [widoku](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="e5c7b-433">`ViewLocalizer`Klasa implementuje ten interfejs i odnajduje lokalizację zasobu ze ścieżki pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="e5c7b-434">Poniższy kod pokazuje, jak używać domyślnej implementacji `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="e5c7b-435">Domyślna implementacja programu `IViewLocalizer` znajduje plik zasobów na podstawie nazwy pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="e5c7b-436">Nie ma możliwości użycia globalnego pliku zasobów udostępnionych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="e5c7b-437">`ViewLocalizer`implementuje lokalizatora przy użyciu `IHtmlLocalizer` , dlatego Razor nie KODUJ kodu HTML zlokalizowanego ciągu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="e5c7b-438">Można Sparametryzuj ciągi zasobów i `IViewLocalizer` zakodować w kodzie HTML parametry, ale nie ciąg zasobu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="e5c7b-439">Rozważ następujące Razor oznakowanie:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="e5c7b-440">Plik zasobów francuski może zawierać następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="e5c7b-441">Klucz</span><span class="sxs-lookup"><span data-stu-id="e5c7b-441">Key</span></span> | <span data-ttu-id="e5c7b-442">Wartość</span><span class="sxs-lookup"><span data-stu-id="e5c7b-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="e5c7b-443">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-444">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-445">'Identity'</span></span>
- <span data-ttu-id="e5c7b-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-447">'Razor'</span></span>
- <span data-ttu-id="e5c7b-448">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-448">'SignalR' uid:</span></span> 

<span data-ttu-id="e5c7b-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="e5c7b-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="e5c7b-450">Renderowany widok będzie zawierać znacznik HTML z pliku zasobu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="e5c7b-451">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="e5c7b-452">Aby użyć udostępnionego pliku zasobu w widoku, wstrzyknąć `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="e5c7b-453">Lokalizacja adnotacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-453">DataAnnotations localization</span></span>

<span data-ttu-id="e5c7b-454">Komunikaty o błędach DataAnnotations są zlokalizowane przy użyciu `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="e5c7b-455">Korzystając z opcji `ResourcesPath = "Resources"` , komunikaty o błędach w programie `RegisterViewModel` mogą być przechowywane w jednej z następujących ścieżek:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="e5c7b-456">*Zasoby/modele widoków. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="e5c7b-457">*Zasoby/modele widoków/Account/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="e5c7b-458">W ASP.NET Core MVC 1.1.0 i wyższych atrybuty inne niż Walidacja są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="e5c7b-459">ASP.NET Core MVC 1,0 **nie** wyszukuje zlokalizowanych ciągów dla atrybutów niezwiązanych z walidacją.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="e5c7b-460">Używanie jednego ciągu zasobu dla wielu klas</span><span class="sxs-lookup"><span data-stu-id="e5c7b-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="e5c7b-461">Poniższy kod przedstawia sposób użycia jednego ciągu zasobu do atrybutów walidacji z wieloma klasami:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="e5c7b-462">W poprzednim kodzie, `SharedResource` jest klasą odpowiadającą resx, gdzie są przechowywane Twoje wiadomości weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="e5c7b-463">W tym podejściu DataAnnotations będą używać tylko `SharedResource` , a nie zasobów dla każdej klasy.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="e5c7b-464">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="e5c7b-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="e5c7b-465">SupportedCultures i SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="e5c7b-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="e5c7b-466">ASP.NET Core pozwala określić dwie wartości kulturowe `SupportedCultures` i `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="e5c7b-467">Obiekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) dla `SupportedCultures` określa wyniki funkcji zależnych od kultury, takich jak data, godzina, liczba i formatowanie waluty.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="e5c7b-468">`SupportedCultures`określa również kolejność sortowania tekstu, Konwencji wielkości liter i porównań ciągów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="e5c7b-469">Zobacz [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) , aby uzyskać więcej informacji na temat sposobu, w jaki serwer pobiera kulturę.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="e5c7b-470">`SupportedUICultures`Określa, które przetłumaczone ciągi (z plików *resx* ) są wyszukiwane przez program [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="e5c7b-471">`ResourceManager`Po prostu wyszukuje ciągi specyficzne dla kultury, które są określane przez `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="e5c7b-472">Każdy wątek w programie .NET `CurrentCulture` ma `CurrentUICulture` obiekty i.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="e5c7b-473">ASP.NET Core sprawdza te wartości podczas renderowania funkcji zależnych od kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="e5c7b-474">Na przykład, jeśli kultura bieżącego wątku jest ustawiona na wartość "en-US" (angielski, Stany Zjednoczone), `DateTime.Now.ToLongDateString()` zostanie wyświetlona wartość "czwartek, 18 lutego, 2016", ale jeśli `CurrentCulture` jest ustawiona na "ES-es" (hiszpański, Hiszpania) dane wyjściowe będą "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="e5c7b-475">Pliki zasobów</span><span class="sxs-lookup"><span data-stu-id="e5c7b-475">Resource files</span></span>

<span data-ttu-id="e5c7b-476">Plik zasobów jest użytecznym mechanizmem oddzielania lokalizowalnych ciągów od kodu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="e5c7b-477">Przetłumaczone ciągi dla języka innego niż domyślny są izolowane w plikach zasobów *resx* .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="e5c7b-478">Na przykład możesz chcieć utworzyć plik zasobów hiszpański o nazwie *Welcome. es. resx* zawierający przetłumaczone ciągi.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="e5c7b-479">"es" to kod języka w języku hiszpańskim.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="e5c7b-480">Aby utworzyć ten plik zasobów w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="e5c7b-481">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder, który będzie zawierać plik zasobów, > **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Zagnieżdżone menu kontekstowe: w Eksplorator rozwiązań menu kontekstowe jest otwarte dla zasobów.](localization/_static/newi.png)

2. <span data-ttu-id="e5c7b-484">W polu **wyszukiwania zainstalowanych szablonów** wprowadź "Resource" i Nazwij plik.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Okno dialogowe Dodawanie nowego elementu](localization/_static/res.png)

3. <span data-ttu-id="e5c7b-486">Wprowadź wartość klucza (ciąg macierzysty) w kolumnie **Nazwa** i przetłumaczony ciąg w kolumnie **wartość** .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome. es. resx plik (plik zasobów powitalnych dla języka hiszpańskiego) z słowem Hello w kolumnie Nazwa i słowem Hola (Hello w języku hiszpańskim) w kolumnie wartość](localization/_static/hola.png)

    <span data-ttu-id="e5c7b-488">Program Visual Studio wyświetla plik *Welcome. es. resx* .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Eksplorator rozwiązań przedstawiający plik zasobów programu Welcome hiszpański (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="e5c7b-490">Nazewnictwo plików zasobów</span><span class="sxs-lookup"><span data-stu-id="e5c7b-490">Resource file naming</span></span>

<span data-ttu-id="e5c7b-491">Zasoby są nazwane dla pełnej nazwy typu swojej klasy pomniejszonej o nazwę zestawu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="e5c7b-492">Na przykład zasób francuski w projekcie, którego głównym zestawem jest `LocalizationWebsite.Web.dll` dla klasy, będzie `LocalizationWebsite.Web.Startup` nazwany *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="e5c7b-493">Zasób klasy `LocalizationWebsite.Web.Controllers.HomeController` nosi nazwę *controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-494">Jeśli przestrzeń nazw klasy Target nie jest taka sama jak nazwa zestawu, będzie potrzebna pełna nazwa typu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="e5c7b-495">Przykładowo w przykładowym projekcie zasób dla typu `ExtraNamespace.Tools` powinien mieć nazwę *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="e5c7b-496">W przykładowym projekcie `ConfigureServices` Metoda ustawia `ResourcesPath` do "zasobów", więc ścieżka względna projektu dla francuskiego pliku zasobów kontrolera głównego to *zasoby/kontrolery. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-497">Alternatywnie można użyć folderów do organizowania plików zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="e5c7b-498">W przypadku kontrolera macierzystego ścieżka będzie zawierać *zasoby/kontrolery/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-499">Jeśli ta opcja nie zostanie użyta `ResourcesPath` , plik *. resx* zostanie użyty w katalogu bazowym projektu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="e5c7b-500">Plik zasobu dla `HomeController` nosi nazwę *controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-501">Wybór przy użyciu konwencji nazewnictwa kropka lub ścieżki zależy od tego, w jaki sposób chcesz zorganizować pliki zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="e5c7b-502">Nazwa zasobu</span><span class="sxs-lookup"><span data-stu-id="e5c7b-502">Resource name</span></span> | <span data-ttu-id="e5c7b-503">Nazwa kropka lub ścieżki</span><span class="sxs-lookup"><span data-stu-id="e5c7b-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="e5c7b-504">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-505">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-506">'Identity'</span></span>
- <span data-ttu-id="e5c7b-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-508">'Razor'</span></span>
- <span data-ttu-id="e5c7b-509">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-510">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-511">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-512">'Identity'</span></span>
- <span data-ttu-id="e5c7b-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-514">'Razor'</span></span>
- <span data-ttu-id="e5c7b-515">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-516">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-517">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-518">'Identity'</span></span>
- <span data-ttu-id="e5c7b-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-520">'Razor'</span></span>
- <span data-ttu-id="e5c7b-521">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-522">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-523">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-524">'Identity'</span></span>
- <span data-ttu-id="e5c7b-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-526">'Razor'</span></span>
- <span data-ttu-id="e5c7b-527">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-527">'SignalR' uid:</span></span> 

<span data-ttu-id="e5c7b-528">------   | ---title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-529">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-530">'Identity'</span></span>
- <span data-ttu-id="e5c7b-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-532">'Razor'</span></span>
- <span data-ttu-id="e5c7b-533">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-534">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-535">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-536">'Identity'</span></span>
- <span data-ttu-id="e5c7b-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-538">'Razor'</span></span>
- <span data-ttu-id="e5c7b-539">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-540">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-541">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-542">'Identity'</span></span>
- <span data-ttu-id="e5c7b-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-544">'Razor'</span></span>
- <span data-ttu-id="e5c7b-545">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-546">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-547">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-548">'Identity'</span></span>
- <span data-ttu-id="e5c7b-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-550">'Razor'</span></span>
- <span data-ttu-id="e5c7b-551">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-551">'SignalR' uid:</span></span> 

<span data-ttu-id="e5c7b-552">------- | | Zasoby/kontrolery. HomeController. fr. resx | Kropka | | Zasoby/kontrolery/HomeController. fr. resx | Ścieżka | |    |     |</span><span class="sxs-lookup"><span data-stu-id="e5c7b-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="e5c7b-553">Pliki zasobów używające `@inject IViewLocalizer` w Razor widokach są zgodne z podobnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="e5c7b-554">Plik zasobów dla widoku może być nazwany przy użyciu nazw kropek lub nazw ścieżek.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-554">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="e5c7b-555">Wyświetl pliki zasobów, aby naśladować ścieżkę skojarzonego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-555"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="e5c7b-556">Przy założeniu, że ustawimy `ResourcesPath` na "zasoby", plik zasobów francuski skojarzony z widokiem */Home/about. cshtml* może mieć jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="e5c7b-557">Zasoby/widoki/Strona główna/informacje. fr. resx</span><span class="sxs-lookup"><span data-stu-id="e5c7b-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="e5c7b-558">Zasoby/widoki. Strona główna. informacje. fr. resx</span><span class="sxs-lookup"><span data-stu-id="e5c7b-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="e5c7b-559">Jeśli opcja nie zostanie użyta `ResourcesPath` , plik *resx* dla widoku powinien znajdować się w tym samym folderze co widok.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="e5c7b-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="e5c7b-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="e5c7b-561">Atrybut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) udostępnia główną przestrzeń nazw zestawu, gdy główna przestrzeń nazw zestawu jest inna niż nazwa zestawu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="e5c7b-562">Taka sytuacja może wystąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="e5c7b-563">Na przykład `my-project-name.csproj` użyje głównej przestrzeni nazw `my_project_name` i nazwy zestawu `my-project-name` prowadzącej do tego błędu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="e5c7b-564">Jeśli główna przestrzeń nazw zestawu jest inna niż nazwa zestawu:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="e5c7b-565">Lokalizacja nie działa domyślnie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-565">Localization does not work by default.</span></span>
* <span data-ttu-id="e5c7b-566">Lokalizowanie nie powiedzie się z powodu sposobu wyszukiwania zasobów w zestawie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="e5c7b-567">`RootNamespace`jest wartością czasu kompilacji, która nie jest dostępna dla wykonywanego procesu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="e5c7b-568">Jeśli `RootNamespace` różni się od `AssemblyName` , należy uwzględnić następujące w *AssemblyInfo.cs* (z wartościami parametrów zamienionymi na wartości rzeczywiste):</span><span class="sxs-lookup"><span data-stu-id="e5c7b-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="e5c7b-569">Poprzedni kod umożliwia pomyślne rozpoznanie plików resx.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="e5c7b-570">Zachowanie rezerwowe kultury</span><span class="sxs-lookup"><span data-stu-id="e5c7b-570">Culture fallback behavior</span></span>

<span data-ttu-id="e5c7b-571">Podczas wyszukiwania zasobu lokalizacja prowadzi do "rezerwy kulturowej".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="e5c7b-572">Jeśli nie zostanie znaleziona, rozpoczyna się od żądanej kultury, przywraca kulturę nadrzędną tej kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="e5c7b-573">Poza tym Właściwość [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) reprezentuje kulturę nadrzędną.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="e5c7b-574">Zwykle (ale nie zawsze) oznacza usunięcie z ISO.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="e5c7b-575">Na przykład dialekt języka hiszpańskiego wymawianego w Meksyku to "es-MX".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="e5c7b-576">Jest to wersja Hiszpańska "es", która &mdash; nie jest specyficzna dla żadnego z krajów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="e5c7b-577">Wyobraź sobie, że witryna otrzymuje żądanie dotyczące zasobu "Welcome" przy użyciu kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="e5c7b-578">System lokalizacji wyszukuje następujące zasoby w kolejności i wybiera pierwsze dopasowanie:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="e5c7b-579">*Welcome.fr — CA. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="e5c7b-580">*Witamy. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="e5c7b-581">*Welcome. resx* (Jeśli `NeutralResourcesLanguage` jest "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="e5c7b-582">Na przykład, jeśli usuniesz oznaczenie kultury ". fr" i masz kulturę ustawioną na francuski, domyślny plik zasobów jest odczytywany, a ciągi są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="e5c7b-583">Menedżer zasobów określa domyślny lub rezerwowy zasób, gdy nic nie spełnia wymaganej kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="e5c7b-584">Jeśli chcesz po prostu zwrócić klucz, gdy brakuje zasobu dla wymaganej kultury, nie musisz mieć domyślnego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="e5c7b-585">Generowanie plików zasobów przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5c7b-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="e5c7b-586">Jeśli utworzysz plik zasobów w programie Visual Studio bez kultury w nazwie pliku (na przykład *Welcome. resx*), program Visual Studio utworzy klasę języka C# z właściwością dla każdego ciągu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="e5c7b-587">Zwykle nie jest to możliwe dzięki ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="e5c7b-588">Zazwyczaj nie istnieje domyślny plik zasobów *resx* (plik *. resx* bez nazwy kultury).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="e5c7b-589">Zalecamy utworzenie pliku *resx* z nazwą kultury (na przykład *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="e5c7b-590">Podczas tworzenia pliku *resx* przy użyciu nazwy kultury program Visual Studio nie generuje pliku klasy.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="e5c7b-591">Dodaj inne kultury</span><span class="sxs-lookup"><span data-stu-id="e5c7b-591">Add other cultures</span></span>

<span data-ttu-id="e5c7b-592">Każda kombinacja języka i kultury (oprócz języka domyślnego) wymaga unikatowego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="e5c7b-593">Tworzysz pliki zasobów dla różnych kultur i ustawień regionalnych, tworząc nowe pliki zasobów, w których kody języka ISO są częścią nazwy pliku (na przykład **en-us**, **fr-CA**i **pl-GB**).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="e5c7b-594">Te kody ISO są umieszczane między nazwami plików i rozszerzeniem *resx* , jak w *Welcome.es-MX. resx* (hiszpański/Meksyk).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="e5c7b-595">Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="e5c7b-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="e5c7b-596">Konfigurowanie lokalizacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-596">Configure localization</span></span>

<span data-ttu-id="e5c7b-597">Lokalizacja jest skonfigurowana w ramach `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="e5c7b-598">`AddLocalization`Dodaje usługi lokalizacyjne do kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="e5c7b-599">Powyższy kod również ustawia ścieżkę zasobów na "zasoby".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="e5c7b-600">`AddViewLocalization`Dodaje obsługę zlokalizowanych plików widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="e5c7b-601">Ta lokalizacja widoku przykładowego jest oparta na sufiksie pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="e5c7b-602">Na przykład "fr" w pliku *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="e5c7b-603">`AddDataAnnotationsLocalization`Dodaje obsługę zlokalizowanych `DataAnnotations` komunikatów weryfikacyjnych za pomocą `IStringLocalizer` abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="e5c7b-604">Oprogramowanie pośredniczące lokalizacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-604">Localization middleware</span></span>

<span data-ttu-id="e5c7b-605">Bieżąca kultura w żądaniu jest ustawiana w oprogramowaniu [pośredniczącym](xref:fundamentals/middleware/index)lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="e5c7b-606">Oprogramowanie pośredniczące lokalizacji jest włączone w tej `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="e5c7b-607">Oprogramowanie pośredniczące lokalizacyjne musi być skonfigurowane przed jakimkolwiek oprogramowanie pośredniczące, które może sprawdzić kulturę żądania (na przykład `app.UseMvcWithDefaultRoute()` ).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e5c7b-608">`UseRequestLocalization`Inicjuje `RequestLocalizationOptions` obiekt.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="e5c7b-609">Na każdym zażądaniu listy `RequestCultureProvider` w programie `RequestLocalizationOptions` jest wyliczany, a pierwszy dostawca, który może pomyślnie ustalić kulturę żądań, jest używany.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="e5c7b-610">Dostawcy domyślnie pochodzą z `RequestLocalizationOptions` klasy:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="e5c7b-611">Lista domyślna przechodzi od najbardziej konkretnych do najmniej określonych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="e5c7b-612">W dalszej części artykułu zobaczymy, jak można zmienić kolejność, a nawet dodać niestandardowego dostawcę kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="e5c7b-613">Jeśli żaden z dostawców nie może określić kultury żądania, `DefaultRequestCulture` jest używana.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="e5c7b-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e5c7b-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="e5c7b-615">Niektóre aplikacje będą używać ciągu zapytania w celu ustawienia kultur [i kultury interfejsu użytkownika](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="e5c7b-616">W przypadku aplikacji korzystających z metody pliku cookie lub z nagłówka Accept-Language Dodawanie ciągu zapytania do adresu URL jest przydatne w przypadku debugowania i testowania kodu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="e5c7b-617">Domyślnie program `QueryStringRequestCultureProvider` jest rejestrowany jako pierwszy dostawca lokalizacji na `RequestCultureProvider` liście.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="e5c7b-618">Parametry ciągu zapytania są przekazywane `culture` i `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="e5c7b-619">Poniższy przykład ustawia określoną kulturę (język i region) na hiszpański/Meksyk:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="e5c7b-620">Jeśli przekazujesz tylko jeden z dwóch ( `culture` lub `ui-culture` ), dostawca ciągu zapytania ustawi obie wartości przy użyciu przekazanego elementu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="e5c7b-621">Na przykład ustawienie tylko kulturowe ustawi zarówno, `Culture` jak i `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="e5c7b-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e5c7b-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="e5c7b-623">Aplikacje produkcyjne często udostępniają mechanizm ustawiania kultury przy użyciu pliku cookie ASP.NET Core kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="e5c7b-624">Użyj `MakeCookieValue` metody, aby utworzyć plik cookie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="e5c7b-625">`CookieRequestCultureProvider` `DefaultCookieName` Zwraca domyślną nazwę pliku cookie używaną do śledzenia preferowanych informacji o kulturze użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="e5c7b-626">Domyślna nazwa pliku cookie to `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="e5c7b-627">Format pliku cookie to `c=%LANGCODE%|uic=%LANGCODE%` , gdzie `c` is `Culture` i `uic` is `UICulture` , na przykład:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="e5c7b-628">Jeśli określisz tylko jedną z informacji o kulturze i kulturze interfejsu użytkownika, określona kultura zostanie użyta dla informacji kultury i kultury interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="e5c7b-629">Nagłówek Accept-Language HTTP</span><span class="sxs-lookup"><span data-stu-id="e5c7b-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="e5c7b-630">[Nagłówek Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) jest settable w większości przeglądarek i był pierwotnie przeznaczony do określenia języka użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="e5c7b-631">To ustawienie wskazuje, co przeglądarka została ustawiona do wysłania lub która dziedziczy z bazowego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="e5c7b-632">Nagłówek Accept-Language HTTP z żądania przeglądarki nie jest infallibleym sposobem wykrywania preferowanego języka użytkownika (zobacz [Ustawianie preferencji językowych w przeglądarce](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="e5c7b-633">Aplikacja produkcyjna powinna uwzględniać sposób, w jaki użytkownik może dostosować wybór kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="e5c7b-634">Ustawianie nagłówka HTTP Accept-Language w programie IE</span><span class="sxs-lookup"><span data-stu-id="e5c7b-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="e5c7b-635">Na ikonie koła zębatego naciśnij pozycję **Opcje internetowe**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="e5c7b-636">Naciśnij pozycję **Języki**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-636">Tap **Languages**.</span></span>

    ![Opcje internetowe](localization/_static/lang.png)

3. <span data-ttu-id="e5c7b-638">Naciśnij pozycję **Ustaw preferencje językowe**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="e5c7b-639">Naciśnij pozycję **Dodaj język**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="e5c7b-640">Dodaj język.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-640">Add the language.</span></span>

6. <span data-ttu-id="e5c7b-641">Naciśnij pozycję język, a następnie naciśnij pozycję **Przenieś w górę**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="e5c7b-642">Używanie dostawcy niestandardowego</span><span class="sxs-lookup"><span data-stu-id="e5c7b-642">Use a custom provider</span></span>

<span data-ttu-id="e5c7b-643">Załóżmy, że chcesz zezwolić klientom na przechowywanie w swoich bazach danych językowych i kulturowych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="e5c7b-644">Można napisać dostawcę, aby wyszukać te wartości dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="e5c7b-645">Poniższy kod pokazuje, jak dodać niestandardowego dostawcę:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-645">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="e5c7b-646">Służy `RequestLocalizationOptions` do dodawania lub usuwania dostawców lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="e5c7b-647">Ustaw kulturę programowo</span><span class="sxs-lookup"><span data-stu-id="e5c7b-647">Set the culture programmatically</span></span>

<span data-ttu-id="e5c7b-648">Ten przykład **lokalizacji. StarterWeb** projekt w witrynie [GitHub](https://github.com/aspnet/entropy) zawiera interfejs użytkownika służący do ustawiania `Culture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="e5c7b-649">Plik *views/Shared/_SelectLanguagePartial. cshtml* umożliwia wybranie kultury z listy obsługiwanych kultur:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="e5c7b-650">Plik *views/Shared/_SelectLanguagePartial. cshtml* zostanie dodany do `footer` sekcji pliku układu, więc będzie dostępny dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="e5c7b-651">`SetLanguage`Metoda ustawia plik cookie kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="e5c7b-652">Nie można podłączyć *_SelectLanguagePartial. cshtml* do przykładowego kodu dla tego projektu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="e5c7b-653">Projekt **Lokalizacja. StarterWeb** w usłudze [GitHub](https://github.com/aspnet/entropy) ma kod, który umożliwia `RequestLocalizationOptions` Razor Przechodzenie do częściowej przez kontener [iniekcji zależności](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="e5c7b-654">Dane tras powiązań modelu i ciągi zapytania</span><span class="sxs-lookup"><span data-stu-id="e5c7b-654">Model binding route data and query strings</span></span>

<span data-ttu-id="e5c7b-655">Zobacz [zachowanie globalizacji powiązań modelu powiązanie danych i ciągi zapytań](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="e5c7b-656">Warunki globalizacji i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="e5c7b-656">Globalization and localization terms</span></span>

<span data-ttu-id="e5c7b-657">Proces lokalizowania aplikacji wymaga również podstawowej znajomości odpowiednich zestawów znaków, które są często używane w nowoczesnych opracowywaniu oprogramowania i zrozumieniu skojarzonych z nimi problemów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="e5c7b-658">Mimo że wszystkie komputery przechowują tekst jako cyfry (kody), różne systemy przechowują ten sam tekst przy użyciu różnych liczb.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="e5c7b-659">Proces lokalizowania dotyczy tłumaczenia interfejsu użytkownika aplikacji (UI) dla określonych kultur/ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="e5c7b-660">Możliwość [lokalizowania](/dotnet/standard/globalization-localization/localizability-review) to proces pośredni służący do sprawdzania, czy aplikacja globalna jest gotowa do lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="e5c7b-661">Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) dla nazwy kultury to `<languagecode2>-<country/regioncode2>` , gdzie `<languagecode2>` jest kodem języka i `<country/regioncode2>` jest kodem podkultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="e5c7b-662">Na przykład `es-CL` w przypadku języka hiszpańskiego (Chile) `en-US` dla języka angielskiego (Stany Zjednoczone) i `en-AU` dla języka angielskiego (Australia).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="e5c7b-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) jest kombinacją kodu ISO 639 2 litery małymi literami związanymi z językiem i ISO 3166 2 literą w postaci wielkiej litery, skojarzonej z krajem lub regionem.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="e5c7b-664">Zobacz [nazwa kultury języka](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="e5c7b-665">Międzynarodowe jest często skracane do "I18N".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="e5c7b-666">Skrót przyjmuje pierwszą i ostatnią literę oraz liczbę liter między nimi, więc 18 oznacza liczbę liter między pierwszym "I" i ostatnim "N".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="e5c7b-667">Dotyczy to zarówno globalizacji (G11N), jak i lokalizacji (L10N).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="e5c7b-668">Odsetk</span><span class="sxs-lookup"><span data-stu-id="e5c7b-668">Terms:</span></span>

* <span data-ttu-id="e5c7b-669">Globalizacja (G11N): proces tworzenia aplikacji w różnych językach i regionach.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="e5c7b-670">Lokalizacja (L10N): proces dostosowywania aplikacji dla danego języka i regionu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="e5c7b-671">Międzynarodowe (I18N): opisuje zarówno globalizację, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="e5c7b-672">Kultura: jest to język i, opcjonalnie, region.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="e5c7b-673">Kultura neutralna: kultura, która ma określony język, ale nie region.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="e5c7b-674">(na przykład "en", "es")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-674">(for example "en", "es")</span></span>
* <span data-ttu-id="e5c7b-675">Określona kultura: kultura, która ma określony język i region.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="e5c7b-676">(na przykład "en-US", "pl-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="e5c7b-677">Kultura nadrzędna: neutralna kultura, która zawiera określoną kulturę.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="e5c7b-678">(na przykład "en" jest kulturą nadrzędną wartości "pl-US" i "pl-GB")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="e5c7b-679">Ustawienia regionalne: ustawienie regionalne jest takie samo jak kultura.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="e5c7b-680">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="e5c7b-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="e5c7b-681">[Projekt lokalizacji. StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) używany w artykule.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="e5c7b-682">Globalizacja i lokalizowanie aplikacji platformy .NET</span><span class="sxs-lookup"><span data-stu-id="e5c7b-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="e5c7b-683">Zasoby w plikach resx</span><span class="sxs-lookup"><span data-stu-id="e5c7b-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="e5c7b-684">Zestaw Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="e5c7b-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="e5c7b-685">Lokalizacje & typy ogólne</span><span class="sxs-lookup"><span data-stu-id="e5c7b-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="e5c7b-686">[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)i [Hisham bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="e5c7b-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="e5c7b-687">Witryna internetowa w wielu językach umożliwia dostęp do szerszego grona użytkowników.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="e5c7b-688">ASP.NET Core udostępnia usługi i oprogramowanie pośredniczące do lokalizowania w różnych językach i kulturach.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="e5c7b-689">Między innymi są używane [globalizacji](/dotnet/api/system.globalization) i [lokalizacje](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="e5c7b-690">Globalizacja to proces projektowania aplikacji, które obsługują różne kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="e5c7b-691">Globalizacja dodaje obsługę danych wejściowych, wyświetlanych i wyjściowych zdefiniowanego zestawu skryptów języka, które odnoszą się do określonych obszarów geograficznych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="e5c7b-692">Lokalizacja to proces adaptacji aplikacji, która została już przetworzona w celu zlokalizowania, do określonej kultury lub ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="e5c7b-693">Aby uzyskać więcej informacji **, zobacz sekcję globalizacja i warunki lokalizacji** na końcu tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="e5c7b-694">Lokalizacja aplikacji obejmuje następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-694">App localization involves the following:</span></span>

1. <span data-ttu-id="e5c7b-695">Ustaw lokalizowalność zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="e5c7b-696">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="e5c7b-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="e5c7b-697">Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="e5c7b-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="e5c7b-698">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e5c7b-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="e5c7b-699">Ustaw lokalizowalność zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-699">Make the app's content localizable</span></span>

<span data-ttu-id="e5c7b-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' nie wymaga zapisywania w pliku zasobów ciągów języka domyślnego.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="e5c7b-701">Możesz tworzyć aplikacje przeznaczone do lokalizacji i nie musisz już tworzyć plików zasobów w fazie opracowywania.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="e5c7b-702">Poniższy kod przedstawia sposób zawijania ciągu "informacje o tytule" dla lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="e5c7b-703">W poprzednim kodzie `IStringLocalizer<T>` implementacja pochodzi z [iniekcji zależności](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="e5c7b-704">Jeśli zlokalizowana wartość "informacje o tytule" nie zostanie znaleziona, zostanie zwrócony klucz indeksatora, czyli ciąg "informacje o tytule".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="e5c7b-705">Możesz pozostawić domyślne ciągi literałów języka w aplikacji i otoczyć je w lokalizatorze, aby można było skupić się na tworzeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="e5c7b-706">Tworzysz aplikację przy użyciu języka domyślnego i przygotujesz ją do kroku lokalizacji bez wcześniejszego tworzenia domyślnego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="e5c7b-707">Alternatywnie można użyć tradycyjnego podejścia i podać klucz do pobrania domyślnego ciągu języka.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="e5c7b-708">Dla wielu deweloperów nowy przepływ pracy nie ma domyślnego pliku języka *. resx* i po prostu zawijający literały ciągu może zmniejszyć obciążenie lokalizowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="e5c7b-709">Inni deweloperzy będą wolą tradycyjne przepływy pracy, ponieważ ułatwiają one pracę z dłuższymi literałami ciągów i ułatwiają aktualizowanie zlokalizowanych ciągów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="e5c7b-710">Użyj `IHtmlLocalizer<T>` implementacji dla zasobów, które zawierają kod HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="e5c7b-711">`IHtmlLocalizer`KOD HTML koduje argumenty, które są sformatowane w ciągu zasobu, ale nie kodu HTML samego samego ciągu zasobu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="e5c7b-712">W przykładzie wyróżnionym poniżej tylko wartość `name` parametru jest zakodowana w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="e5c7b-713">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="e5c7b-714">Na najniższym poziomie można uzyskać możliwość `IStringLocalizerFactory` [iniekcji zależności](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="e5c7b-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="e5c7b-715">Powyższy kod demonstruje każdą z dwóch metod tworzenia fabryk.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="e5c7b-716">Zlokalizowane ciągi można podzielić według kontrolera, obszaru lub tylko jednego kontenera.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="e5c7b-717">W aplikacji przykładowej Klasa fikcyjna o nazwie `SharedResource` jest używana do współużytkowanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="e5c7b-718">Niektórzy Deweloperzy używają `Startup` klasy, aby zawierały ciągi globalne lub udostępnione.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="e5c7b-719">W poniższym przykładzie `InfoController` `SharedResource` są używane i lokalizatory:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="e5c7b-720">Wyświetl lokalizację</span><span class="sxs-lookup"><span data-stu-id="e5c7b-720">View localization</span></span>

<span data-ttu-id="e5c7b-721">`IViewLocalizer`Usługa udostępnia zlokalizowane ciągi dla [widoku](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="e5c7b-722">`ViewLocalizer`Klasa implementuje ten interfejs i odnajduje lokalizację zasobu ze ścieżki pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="e5c7b-723">Poniższy kod pokazuje, jak używać domyślnej implementacji `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="e5c7b-724">Domyślna implementacja programu `IViewLocalizer` znajduje plik zasobów na podstawie nazwy pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="e5c7b-725">Nie ma możliwości użycia globalnego pliku zasobów udostępnionych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="e5c7b-726">`ViewLocalizer`implementuje lokalizatora przy użyciu `IHtmlLocalizer` , dlatego Razor nie KODUJ kodu HTML zlokalizowanego ciągu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="e5c7b-727">Można Sparametryzuj ciągi zasobów i `IViewLocalizer` zakodować w kodzie HTML parametry, ale nie ciąg zasobu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="e5c7b-728">Rozważ następujące Razor oznakowanie:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="e5c7b-729">Plik zasobów francuski może zawierać następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="e5c7b-730">Klucz</span><span class="sxs-lookup"><span data-stu-id="e5c7b-730">Key</span></span> | <span data-ttu-id="e5c7b-731">Wartość</span><span class="sxs-lookup"><span data-stu-id="e5c7b-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="e5c7b-732">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-733">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-734">'Identity'</span></span>
- <span data-ttu-id="e5c7b-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-736">'Razor'</span></span>
- <span data-ttu-id="e5c7b-737">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-737">'SignalR' uid:</span></span> 

<span data-ttu-id="e5c7b-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="e5c7b-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="e5c7b-739">Renderowany widok będzie zawierać znacznik HTML z pliku zasobu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="e5c7b-740">**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="e5c7b-741">Aby użyć udostępnionego pliku zasobu w widoku, wstrzyknąć `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="e5c7b-742">Lokalizacja adnotacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-742">DataAnnotations localization</span></span>

<span data-ttu-id="e5c7b-743">Komunikaty o błędach DataAnnotations są zlokalizowane przy użyciu `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="e5c7b-744">Korzystając z opcji `ResourcesPath = "Resources"` , komunikaty o błędach w programie `RegisterViewModel` mogą być przechowywane w jednej z następujących ścieżek:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="e5c7b-745">*Zasoby/modele widoków. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="e5c7b-746">*Zasoby/modele widoków/Account/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="e5c7b-747">W ASP.NET Core MVC 1.1.0 i wyższych atrybuty inne niż Walidacja są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="e5c7b-748">ASP.NET Core MVC 1,0 **nie** wyszukuje zlokalizowanych ciągów dla atrybutów niezwiązanych z walidacją.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="e5c7b-749">Używanie jednego ciągu zasobu dla wielu klas</span><span class="sxs-lookup"><span data-stu-id="e5c7b-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="e5c7b-750">Poniższy kod przedstawia sposób użycia jednego ciągu zasobu do atrybutów walidacji z wieloma klasami:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="e5c7b-751">W poprzednim kodzie, `SharedResource` jest klasą odpowiadającą resx, gdzie są przechowywane Twoje wiadomości weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="e5c7b-752">W tym podejściu DataAnnotations będą używać tylko `SharedResource` , a nie zasobów dla każdej klasy.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="e5c7b-753">Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur</span><span class="sxs-lookup"><span data-stu-id="e5c7b-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="e5c7b-754">SupportedCultures i SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="e5c7b-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="e5c7b-755">ASP.NET Core pozwala określić dwie wartości kulturowe `SupportedCultures` i `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="e5c7b-756">Obiekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) dla `SupportedCultures` określa wyniki funkcji zależnych od kultury, takich jak data, godzina, liczba i formatowanie waluty.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="e5c7b-757">`SupportedCultures`określa również kolejność sortowania tekstu, Konwencji wielkości liter i porównań ciągów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="e5c7b-758">Zobacz [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) , aby uzyskać więcej informacji na temat sposobu, w jaki serwer pobiera kulturę.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="e5c7b-759">`SupportedUICultures`Określa, które przetłumaczone ciągi (z plików *resx* ) są wyszukiwane przez program [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="e5c7b-760">`ResourceManager`Po prostu wyszukuje ciągi specyficzne dla kultury, które są określane przez `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="e5c7b-761">Każdy wątek w programie .NET `CurrentCulture` ma `CurrentUICulture` obiekty i.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="e5c7b-762">ASP.NET Core sprawdza te wartości podczas renderowania funkcji zależnych od kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="e5c7b-763">Na przykład, jeśli kultura bieżącego wątku jest ustawiona na wartość "en-US" (angielski, Stany Zjednoczone), `DateTime.Now.ToLongDateString()` zostanie wyświetlona wartość "czwartek, 18 lutego, 2016", ale jeśli `CurrentCulture` jest ustawiona na "ES-es" (hiszpański, Hiszpania) dane wyjściowe będą "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="e5c7b-764">Pliki zasobów</span><span class="sxs-lookup"><span data-stu-id="e5c7b-764">Resource files</span></span>

<span data-ttu-id="e5c7b-765">Plik zasobów jest użytecznym mechanizmem oddzielania lokalizowalnych ciągów od kodu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="e5c7b-766">Przetłumaczone ciągi dla języka innego niż domyślny są izolowane w plikach zasobów *resx* .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="e5c7b-767">Na przykład możesz chcieć utworzyć plik zasobów hiszpański o nazwie *Welcome. es. resx* zawierający przetłumaczone ciągi.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="e5c7b-768">"es" to kod języka w języku hiszpańskim.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="e5c7b-769">Aby utworzyć ten plik zasobów w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="e5c7b-770">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder, który będzie zawierać plik zasobów, > **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Zagnieżdżone menu kontekstowe: w Eksplorator rozwiązań menu kontekstowe jest otwarte dla zasobów.](localization/_static/newi.png)

2. <span data-ttu-id="e5c7b-773">W polu **wyszukiwania zainstalowanych szablonów** wprowadź "Resource" i Nazwij plik.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Okno dialogowe Dodawanie nowego elementu](localization/_static/res.png)

3. <span data-ttu-id="e5c7b-775">Wprowadź wartość klucza (ciąg macierzysty) w kolumnie **Nazwa** i przetłumaczony ciąg w kolumnie **wartość** .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome. es. resx plik (plik zasobów powitalnych dla języka hiszpańskiego) z słowem Hello w kolumnie Nazwa i słowem Hola (Hello w języku hiszpańskim) w kolumnie wartość](localization/_static/hola.png)

    <span data-ttu-id="e5c7b-777">Program Visual Studio wyświetla plik *Welcome. es. resx* .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Eksplorator rozwiązań przedstawiający plik zasobów programu Welcome hiszpański (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="e5c7b-779">Nazewnictwo plików zasobów</span><span class="sxs-lookup"><span data-stu-id="e5c7b-779">Resource file naming</span></span>

<span data-ttu-id="e5c7b-780">Zasoby są nazwane dla pełnej nazwy typu swojej klasy pomniejszonej o nazwę zestawu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="e5c7b-781">Na przykład zasób francuski w projekcie, którego głównym zestawem jest `LocalizationWebsite.Web.dll` dla klasy, będzie `LocalizationWebsite.Web.Startup` nazwany *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="e5c7b-782">Zasób klasy `LocalizationWebsite.Web.Controllers.HomeController` nosi nazwę *controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-783">Jeśli przestrzeń nazw klasy Target nie jest taka sama jak nazwa zestawu, będzie potrzebna pełna nazwa typu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="e5c7b-784">Przykładowo w przykładowym projekcie zasób dla typu `ExtraNamespace.Tools` powinien mieć nazwę *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="e5c7b-785">W przykładowym projekcie `ConfigureServices` Metoda ustawia `ResourcesPath` do "zasobów", więc ścieżka względna projektu dla francuskiego pliku zasobów kontrolera głównego to *zasoby/kontrolery. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-786">Alternatywnie można użyć folderów do organizowania plików zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="e5c7b-787">W przypadku kontrolera macierzystego ścieżka będzie zawierać *zasoby/kontrolery/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-788">Jeśli ta opcja nie zostanie użyta `ResourcesPath` , plik *. resx* zostanie użyty w katalogu bazowym projektu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="e5c7b-789">Plik zasobu dla `HomeController` nosi nazwę *controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e5c7b-790">Wybór przy użyciu konwencji nazewnictwa kropka lub ścieżki zależy od tego, w jaki sposób chcesz zorganizować pliki zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="e5c7b-791">Nazwa zasobu</span><span class="sxs-lookup"><span data-stu-id="e5c7b-791">Resource name</span></span> | <span data-ttu-id="e5c7b-792">Nazwa kropka lub ścieżki</span><span class="sxs-lookup"><span data-stu-id="e5c7b-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="e5c7b-793">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-794">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-795">'Identity'</span></span>
- <span data-ttu-id="e5c7b-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-797">'Razor'</span></span>
- <span data-ttu-id="e5c7b-798">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-799">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-800">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-801">'Identity'</span></span>
- <span data-ttu-id="e5c7b-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-803">'Razor'</span></span>
- <span data-ttu-id="e5c7b-804">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-805">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-806">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-807">'Identity'</span></span>
- <span data-ttu-id="e5c7b-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-809">'Razor'</span></span>
- <span data-ttu-id="e5c7b-810">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-811">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-812">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-813">'Identity'</span></span>
- <span data-ttu-id="e5c7b-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-815">'Razor'</span></span>
- <span data-ttu-id="e5c7b-816">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-816">'SignalR' uid:</span></span> 

<span data-ttu-id="e5c7b-817">------   | ---title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-818">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-819">'Identity'</span></span>
- <span data-ttu-id="e5c7b-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-821">'Razor'</span></span>
- <span data-ttu-id="e5c7b-822">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-823">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-824">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-825">'Identity'</span></span>
- <span data-ttu-id="e5c7b-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-827">'Razor'</span></span>
- <span data-ttu-id="e5c7b-828">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-829">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-830">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-831">'Identity'</span></span>
- <span data-ttu-id="e5c7b-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-833">'Razor'</span></span>
- <span data-ttu-id="e5c7b-834">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e5c7b-835">title: Author: Description: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c7b-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-836">'Blazor'</span></span>
- <span data-ttu-id="e5c7b-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-837">'Identity'</span></span>
- <span data-ttu-id="e5c7b-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c7b-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c7b-839">'Razor'</span></span>
- <span data-ttu-id="e5c7b-840">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-840">'SignalR' uid:</span></span> 

<span data-ttu-id="e5c7b-841">------- | | Zasoby/kontrolery. HomeController. fr. resx | Kropka | | Zasoby/kontrolery/HomeController. fr. resx | Ścieżka | |    |     |</span><span class="sxs-lookup"><span data-stu-id="e5c7b-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="e5c7b-842">Pliki zasobów używające `@inject IViewLocalizer` w Razor widokach są zgodne z podobnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="e5c7b-843">Plik zasobów dla widoku może być nazwany przy użyciu nazw kropek lub nazw ścieżek.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-843">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="e5c7b-844">Wyświetl pliki zasobów, aby naśladować ścieżkę skojarzonego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-844"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="e5c7b-845">Przy założeniu, że ustawimy `ResourcesPath` na "zasoby", plik zasobów francuski skojarzony z widokiem */Home/about. cshtml* może mieć jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="e5c7b-846">Zasoby/widoki/Strona główna/informacje. fr. resx</span><span class="sxs-lookup"><span data-stu-id="e5c7b-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="e5c7b-847">Zasoby/widoki. Strona główna. informacje. fr. resx</span><span class="sxs-lookup"><span data-stu-id="e5c7b-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="e5c7b-848">Jeśli opcja nie zostanie użyta `ResourcesPath` , plik *resx* dla widoku powinien znajdować się w tym samym folderze co widok.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="e5c7b-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="e5c7b-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="e5c7b-850">Atrybut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) udostępnia główną przestrzeń nazw zestawu, gdy główna przestrzeń nazw zestawu jest inna niż nazwa zestawu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="e5c7b-851">Taka sytuacja może wystąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="e5c7b-852">Na przykład `my-project-name.csproj` użyje głównej przestrzeni nazw `my_project_name` i nazwy zestawu `my-project-name` prowadzącej do tego błędu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="e5c7b-853">Jeśli główna przestrzeń nazw zestawu jest inna niż nazwa zestawu:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="e5c7b-854">Lokalizacja nie działa domyślnie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-854">Localization does not work by default.</span></span>
* <span data-ttu-id="e5c7b-855">Lokalizowanie nie powiedzie się z powodu sposobu wyszukiwania zasobów w zestawie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="e5c7b-856">`RootNamespace`jest wartością czasu kompilacji, która nie jest dostępna dla wykonywanego procesu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="e5c7b-857">Jeśli `RootNamespace` różni się od `AssemblyName` , należy uwzględnić następujące w *AssemblyInfo.cs* (z wartościami parametrów zamienionymi na wartości rzeczywiste):</span><span class="sxs-lookup"><span data-stu-id="e5c7b-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="e5c7b-858">Poprzedni kod umożliwia pomyślne rozpoznanie plików resx.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="e5c7b-859">Zachowanie rezerwowe kultury</span><span class="sxs-lookup"><span data-stu-id="e5c7b-859">Culture fallback behavior</span></span>

<span data-ttu-id="e5c7b-860">Podczas wyszukiwania zasobu lokalizacja prowadzi do "rezerwy kulturowej".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="e5c7b-861">Jeśli nie zostanie znaleziona, rozpoczyna się od żądanej kultury, przywraca kulturę nadrzędną tej kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="e5c7b-862">Poza tym Właściwość [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) reprezentuje kulturę nadrzędną.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="e5c7b-863">Zwykle (ale nie zawsze) oznacza usunięcie z ISO.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="e5c7b-864">Na przykład dialekt języka hiszpańskiego wymawianego w Meksyku to "es-MX".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="e5c7b-865">Jest to wersja Hiszpańska "es", która &mdash; nie jest specyficzna dla żadnego z krajów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="e5c7b-866">Wyobraź sobie, że witryna otrzymuje żądanie dotyczące zasobu "Welcome" przy użyciu kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="e5c7b-867">System lokalizacji wyszukuje następujące zasoby w kolejności i wybiera pierwsze dopasowanie:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="e5c7b-868">*Welcome.fr — CA. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="e5c7b-869">*Witamy. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="e5c7b-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="e5c7b-870">*Welcome. resx* (Jeśli `NeutralResourcesLanguage` jest "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="e5c7b-871">Na przykład, jeśli usuniesz oznaczenie kultury ". fr" i masz kulturę ustawioną na francuski, domyślny plik zasobów jest odczytywany, a ciągi są zlokalizowane.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="e5c7b-872">Menedżer zasobów określa domyślny lub rezerwowy zasób, gdy nic nie spełnia wymaganej kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="e5c7b-873">Jeśli chcesz po prostu zwrócić klucz, gdy brakuje zasobu dla wymaganej kultury, nie musisz mieć domyślnego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="e5c7b-874">Generowanie plików zasobów przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5c7b-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="e5c7b-875">Jeśli utworzysz plik zasobów w programie Visual Studio bez kultury w nazwie pliku (na przykład *Welcome. resx*), program Visual Studio utworzy klasę języka C# z właściwością dla każdego ciągu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="e5c7b-876">Zwykle nie jest to możliwe dzięki ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="e5c7b-877">Zazwyczaj nie istnieje domyślny plik zasobów *resx* (plik *. resx* bez nazwy kultury).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="e5c7b-878">Zalecamy utworzenie pliku *resx* z nazwą kultury (na przykład *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="e5c7b-879">Podczas tworzenia pliku *resx* przy użyciu nazwy kultury program Visual Studio nie generuje pliku klasy.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="e5c7b-880">Dodaj inne kultury</span><span class="sxs-lookup"><span data-stu-id="e5c7b-880">Add other cultures</span></span>

<span data-ttu-id="e5c7b-881">Każda kombinacja języka i kultury (oprócz języka domyślnego) wymaga unikatowego pliku zasobów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="e5c7b-882">Tworzysz pliki zasobów dla różnych kultur i ustawień regionalnych, tworząc nowe pliki zasobów, w których kody języka ISO są częścią nazwy pliku (na przykład **en-us**, **fr-CA**i **pl-GB**).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="e5c7b-883">Te kody ISO są umieszczane między nazwami plików i rozszerzeniem *resx* , jak w *Welcome.es-MX. resx* (hiszpański/Meksyk).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="e5c7b-884">Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="e5c7b-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="e5c7b-885">Konfigurowanie lokalizacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-885">Configure localization</span></span>

<span data-ttu-id="e5c7b-886">Lokalizacja jest skonfigurowana w ramach `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="e5c7b-887">`AddLocalization`Dodaje usługi lokalizacyjne do kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="e5c7b-888">Powyższy kod również ustawia ścieżkę zasobów na "zasoby".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="e5c7b-889">`AddViewLocalization`Dodaje obsługę zlokalizowanych plików widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="e5c7b-890">Ta lokalizacja widoku przykładowego jest oparta na sufiksie pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="e5c7b-891">Na przykład "fr" w pliku *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="e5c7b-892">`AddDataAnnotationsLocalization`Dodaje obsługę zlokalizowanych `DataAnnotations` komunikatów weryfikacyjnych za pomocą `IStringLocalizer` abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="e5c7b-893">Oprogramowanie pośredniczące lokalizacji</span><span class="sxs-lookup"><span data-stu-id="e5c7b-893">Localization middleware</span></span>

<span data-ttu-id="e5c7b-894">Bieżąca kultura w żądaniu jest ustawiana w oprogramowaniu [pośredniczącym](xref:fundamentals/middleware/index)lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="e5c7b-895">Oprogramowanie pośredniczące lokalizacji jest włączone w tej `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="e5c7b-896">Oprogramowanie pośredniczące lokalizacyjne musi być skonfigurowane przed jakimkolwiek oprogramowanie pośredniczące, które może sprawdzić kulturę żądania (na przykład `app.UseMvcWithDefaultRoute()` ).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e5c7b-897">`UseRequestLocalization`Inicjuje `RequestLocalizationOptions` obiekt.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="e5c7b-898">Na każdym zażądaniu listy `RequestCultureProvider` w programie `RequestLocalizationOptions` jest wyliczany, a pierwszy dostawca, który może pomyślnie ustalić kulturę żądań, jest używany.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="e5c7b-899">Dostawcy domyślnie pochodzą z `RequestLocalizationOptions` klasy:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="e5c7b-900">Lista domyślna przechodzi od najbardziej konkretnych do najmniej określonych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="e5c7b-901">W dalszej części artykułu zobaczymy, jak można zmienić kolejność, a nawet dodać niestandardowego dostawcę kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="e5c7b-902">Jeśli żaden z dostawców nie może określić kultury żądania, `DefaultRequestCulture` jest używana.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="e5c7b-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e5c7b-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="e5c7b-904">Niektóre aplikacje będą używać ciągu zapytania w celu ustawienia kultur [i kultury interfejsu użytkownika](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="e5c7b-905">W przypadku aplikacji korzystających z metody pliku cookie lub z nagłówka Accept-Language Dodawanie ciągu zapytania do adresu URL jest przydatne w przypadku debugowania i testowania kodu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="e5c7b-906">Domyślnie program `QueryStringRequestCultureProvider` jest rejestrowany jako pierwszy dostawca lokalizacji na `RequestCultureProvider` liście.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="e5c7b-907">Parametry ciągu zapytania są przekazywane `culture` i `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="e5c7b-908">Poniższy przykład ustawia określoną kulturę (język i region) na hiszpański/Meksyk:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="e5c7b-909">Jeśli przekazujesz tylko jeden z dwóch ( `culture` lub `ui-culture` ), dostawca ciągu zapytania ustawi obie wartości przy użyciu przekazanego elementu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="e5c7b-910">Na przykład ustawienie tylko kulturowe ustawi zarówno, `Culture` jak i `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="e5c7b-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e5c7b-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="e5c7b-912">Aplikacje produkcyjne często udostępniają mechanizm ustawiania kultury przy użyciu pliku cookie ASP.NET Core kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="e5c7b-913">Użyj `MakeCookieValue` metody, aby utworzyć plik cookie.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="e5c7b-914">`CookieRequestCultureProvider` `DefaultCookieName` Zwraca domyślną nazwę pliku cookie używaną do śledzenia preferowanych informacji o kulturze użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="e5c7b-915">Domyślna nazwa pliku cookie to `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="e5c7b-916">Format pliku cookie to `c=%LANGCODE%|uic=%LANGCODE%` , gdzie `c` is `Culture` i `uic` is `UICulture` , na przykład:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="e5c7b-917">Jeśli określisz tylko jedną z informacji o kulturze i kulturze interfejsu użytkownika, określona kultura zostanie użyta dla informacji kultury i kultury interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="e5c7b-918">Nagłówek Accept-Language HTTP</span><span class="sxs-lookup"><span data-stu-id="e5c7b-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="e5c7b-919">[Nagłówek Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) jest settable w większości przeglądarek i był pierwotnie przeznaczony do określenia języka użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="e5c7b-920">To ustawienie wskazuje, co przeglądarka została ustawiona do wysłania lub która dziedziczy z bazowego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="e5c7b-921">Nagłówek Accept-Language HTTP z żądania przeglądarki nie jest infallibleym sposobem wykrywania preferowanego języka użytkownika (zobacz [Ustawianie preferencji językowych w przeglądarce](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="e5c7b-922">Aplikacja produkcyjna powinna uwzględniać sposób, w jaki użytkownik może dostosować wybór kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="e5c7b-923">Ustawianie nagłówka HTTP Accept-Language w programie IE</span><span class="sxs-lookup"><span data-stu-id="e5c7b-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="e5c7b-924">Na ikonie koła zębatego naciśnij pozycję **Opcje internetowe**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="e5c7b-925">Naciśnij pozycję **Języki**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-925">Tap **Languages**.</span></span>

    ![Opcje internetowe](localization/_static/lang.png)

3. <span data-ttu-id="e5c7b-927">Naciśnij pozycję **Ustaw preferencje językowe**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="e5c7b-928">Naciśnij pozycję **Dodaj język**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="e5c7b-929">Dodaj język.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-929">Add the language.</span></span>

6. <span data-ttu-id="e5c7b-930">Naciśnij pozycję język, a następnie naciśnij pozycję **Przenieś w górę**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="e5c7b-931">Nagłówek HTTP w języku zawartości</span><span class="sxs-lookup"><span data-stu-id="e5c7b-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="e5c7b-932">Nagłówek jednostki [zawartości w języku](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="e5c7b-933">Służy do opisywania języków przeznaczonych dla odbiorców.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="e5c7b-934">Umożliwia użytkownikowi odróżnienie zgodnie z preferowanym językiem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="e5c7b-935">Nagłówki jednostek są używane w żądaniach i odpowiedziach HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="e5c7b-936">`Content-Language`Nagłówek można dodać przez ustawienie właściwości `ApplyCurrentCultureToResponseHeaders` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="e5c7b-937">Dodawanie `Content-Language` nagłówka:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="e5c7b-938">Zezwala RequestLocalizationMiddleware na ustawienie `Content-Language` nagłówka przy użyciu `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="e5c7b-939">Eliminuje konieczność jawnego ustawiania nagłówka odpowiedzi `Content-Language` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="e5c7b-940">Używanie dostawcy niestandardowego</span><span class="sxs-lookup"><span data-stu-id="e5c7b-940">Use a custom provider</span></span>

<span data-ttu-id="e5c7b-941">Załóżmy, że chcesz zezwolić klientom na przechowywanie w swoich bazach danych językowych i kulturowych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="e5c7b-942">Można napisać dostawcę, aby wyszukać te wartości dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="e5c7b-943">Poniższy kod pokazuje, jak dodać niestandardowego dostawcę:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-943">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="e5c7b-944">Służy `RequestLocalizationOptions` do dodawania lub usuwania dostawców lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="e5c7b-945">Ustaw kulturę programowo</span><span class="sxs-lookup"><span data-stu-id="e5c7b-945">Set the culture programmatically</span></span>

<span data-ttu-id="e5c7b-946">Ten przykład **lokalizacji. StarterWeb** projekt w witrynie [GitHub](https://github.com/aspnet/entropy) zawiera interfejs użytkownika służący do ustawiania `Culture` .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="e5c7b-947">Plik *views/Shared/_SelectLanguagePartial. cshtml* umożliwia wybranie kultury z listy obsługiwanych kultur:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="e5c7b-948">Plik *views/Shared/_SelectLanguagePartial. cshtml* zostanie dodany do `footer` sekcji pliku układu, więc będzie dostępny dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="e5c7b-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="e5c7b-949">`SetLanguage`Metoda ustawia plik cookie kultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="e5c7b-950">Nie można podłączyć *_SelectLanguagePartial. cshtml* do przykładowego kodu dla tego projektu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="e5c7b-951">Projekt **Lokalizacja. StarterWeb** w usłudze [GitHub](https://github.com/aspnet/entropy) ma kod, który umożliwia `RequestLocalizationOptions` Razor Przechodzenie do częściowej przez kontener [iniekcji zależności](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="e5c7b-952">Dane tras powiązań modelu i ciągi zapytania</span><span class="sxs-lookup"><span data-stu-id="e5c7b-952">Model binding route data and query strings</span></span>

<span data-ttu-id="e5c7b-953">Zobacz [zachowanie globalizacji powiązań modelu powiązanie danych i ciągi zapytań](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="e5c7b-954">Warunki globalizacji i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="e5c7b-954">Globalization and localization terms</span></span>

<span data-ttu-id="e5c7b-955">Proces lokalizowania aplikacji wymaga również podstawowej znajomości odpowiednich zestawów znaków, które są często używane w nowoczesnych opracowywaniu oprogramowania i zrozumieniu skojarzonych z nimi problemów.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="e5c7b-956">Mimo że wszystkie komputery przechowują tekst jako cyfry (kody), różne systemy przechowują ten sam tekst przy użyciu różnych liczb.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="e5c7b-957">Proces lokalizowania dotyczy tłumaczenia interfejsu użytkownika aplikacji (UI) dla określonych kultur/ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="e5c7b-958">Możliwość [lokalizowania](/dotnet/standard/globalization-localization/localizability-review) to proces pośredni służący do sprawdzania, czy aplikacja globalna jest gotowa do lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="e5c7b-959">Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) dla nazwy kultury to `<languagecode2>-<country/regioncode2>` , gdzie `<languagecode2>` jest kodem języka i `<country/regioncode2>` jest kodem podkultury.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="e5c7b-960">Na przykład `es-CL` w przypadku języka hiszpańskiego (Chile) `en-US` dla języka angielskiego (Stany Zjednoczone) i `en-AU` dla języka angielskiego (Australia).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="e5c7b-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) jest kombinacją kodu ISO 639 2 litery małymi literami związanymi z językiem i ISO 3166 2 literą w postaci wielkiej litery, skojarzonej z krajem lub regionem.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="e5c7b-962">Zobacz [nazwa kultury języka](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="e5c7b-963">Międzynarodowe jest często skracane do "I18N".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="e5c7b-964">Skrót przyjmuje pierwszą i ostatnią literę oraz liczbę liter między nimi, więc 18 oznacza liczbę liter między pierwszym "I" i ostatnim "N".</span><span class="sxs-lookup"><span data-stu-id="e5c7b-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="e5c7b-965">Dotyczy to zarówno globalizacji (G11N), jak i lokalizacji (L10N).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="e5c7b-966">Odsetk</span><span class="sxs-lookup"><span data-stu-id="e5c7b-966">Terms:</span></span>

* <span data-ttu-id="e5c7b-967">Globalizacja (G11N): proces tworzenia aplikacji w różnych językach i regionach.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="e5c7b-968">Lokalizacja (L10N): proces dostosowywania aplikacji dla danego języka i regionu.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="e5c7b-969">Międzynarodowe (I18N): opisuje zarówno globalizację, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="e5c7b-970">Kultura: jest to język i, opcjonalnie, region.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="e5c7b-971">Kultura neutralna: kultura, która ma określony język, ale nie region.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="e5c7b-972">(na przykład "en", "es")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-972">(for example "en", "es")</span></span>
* <span data-ttu-id="e5c7b-973">Określona kultura: kultura, która ma określony język i region.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="e5c7b-974">(na przykład "en-US", "pl-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="e5c7b-975">Kultura nadrzędna: neutralna kultura, która zawiera określoną kulturę.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="e5c7b-976">(na przykład "en" jest kulturą nadrzędną wartości "pl-US" i "pl-GB")</span><span class="sxs-lookup"><span data-stu-id="e5c7b-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="e5c7b-977">Ustawienia regionalne: ustawienie regionalne jest takie samo jak kultura.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="e5c7b-978">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="e5c7b-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="e5c7b-979">[Projekt lokalizacji. StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) używany w artykule.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="e5c7b-980">Globalizacja i lokalizowanie aplikacji platformy .NET</span><span class="sxs-lookup"><span data-stu-id="e5c7b-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="e5c7b-981">Zasoby w plikach resx</span><span class="sxs-lookup"><span data-stu-id="e5c7b-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="e5c7b-982">Zestaw Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="e5c7b-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="e5c7b-983">Lokalizacje & typy ogólne</span><span class="sxs-lookup"><span data-stu-id="e5c7b-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
