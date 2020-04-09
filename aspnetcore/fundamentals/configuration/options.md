---
title: Wzorzec opcji w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać wzorca opcji do reprezentowania grup powiązanych ustawień w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
uid: fundamentals/configuration/options
ms.openlocfilehash: 756d3d57122642ab10ab671c9accb75975c3799d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665460"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="c66aa-103">Wzorzec opcji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c66aa-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c66aa-104">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-104">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="c66aa-105">Gdy [ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza do oddzielnych klas, aplikacja jest zgodna z dwiema ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="c66aa-105">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="c66aa-106">[Zasada segregacji interfejsu (ISP) lub](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scenariusze hermetyzacji (klasy), które zależą od ustawień konfiguracji, zależą tylko od używanych ustawień konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-106">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="c66aa-107">[Separacja dotyczy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Ustawienia dla różnych części aplikacji nie są zależne lub powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="c66aa-107">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="c66aa-108">Opcje zapewniają również mechanizm sprawdzania poprawności danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="c66aa-108">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="c66aa-109">Aby uzyskać więcej informacji, zobacz sekcję [Sprawdzanie poprawności opcji.](#options-validation)</span><span class="sxs-lookup"><span data-stu-id="c66aa-109">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="c66aa-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c66aa-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="c66aa-111">Pakiet</span><span class="sxs-lookup"><span data-stu-id="c66aa-111">Package</span></span>

<span data-ttu-id="c66aa-112">Pakiet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) jest niejawnie odwoływany w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c66aa-112">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="c66aa-113">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="c66aa-113">Options interfaces</span></span>

<span data-ttu-id="c66aa-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania `TOptions` opcjami powiadomień o wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="c66aa-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="c66aa-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="c66aa-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="c66aa-116">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="c66aa-116">Change notifications</span></span>
* [<span data-ttu-id="c66aa-117">Opcje nazwane</span><span class="sxs-lookup"><span data-stu-id="c66aa-117">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="c66aa-118">Konfiguracja z doładowaniem</span><span class="sxs-lookup"><span data-stu-id="c66aa-118">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="c66aa-119">Selektywne unieważnienie<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>opcji ( )</span><span class="sxs-lookup"><span data-stu-id="c66aa-119">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="c66aa-120">[Scenariusze po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę <xref:Microsoft.Extensions.Options.IConfigureOptions%601> opcji po zakończeniu całej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-120">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="c66aa-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych opcji wystąpień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="c66aa-122">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="c66aa-122">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="c66aa-123">Domyślna implementacja <xref:Microsoft.Extensions.Options.IConfigureOptions%601> przyjmuje <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> wszystkie zarejestrowane i uruchamia wszystkie konfiguracje pierwszy, a następnie po konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-123">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="c66aa-124">Rozróżnia <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="c66aa-124">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="c66aa-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> przez `TOptions` do buforowania wystąpień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="c66aa-126">Unieważnia <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> opcje wystąpień na monitorze, tak aby wartość<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>została ponownie skompensowana ( ).</span><span class="sxs-lookup"><span data-stu-id="c66aa-126">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="c66aa-127">Wartości można wprowadzać <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>ręcznie za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="c66aa-127">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="c66aa-128">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> jest używana, gdy wszystkie nazwane wystąpienia powinny być odtworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="c66aa-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie skomputowane na każde żądanie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="c66aa-130">Aby uzyskać więcej informacji, zobacz [reload danych konfiguracyjnych z IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) sekcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-130">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="c66aa-131"><xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-131"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="c66aa-132">Jednak <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje poprzednich scenariuszy . <xref:Microsoft.Extensions.Options.IOptionsMonitor%601></span><span class="sxs-lookup"><span data-stu-id="c66aa-132">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="c66aa-133">Możesz nadal używać <xref:Microsoft.Extensions.Options.IOptions%601> w istniejących strukturach i bibliotekach, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>interfejsu i nie wymagają scenariuszy dostarczonych przez program .</span><span class="sxs-lookup"><span data-stu-id="c66aa-133">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="c66aa-134">Konfiguracja opcji ogólnych</span><span class="sxs-lookup"><span data-stu-id="c66aa-134">General options configuration</span></span>

<span data-ttu-id="c66aa-135">Konfiguracja opcji ogólnych jest pokazana jako przykład 1 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-135">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="c66aa-136">Klasa opcji musi być nie abstrakcyjna z konstruktorem bez parametrów publicznych.</span><span class="sxs-lookup"><span data-stu-id="c66aa-136">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="c66aa-137">Następująca klasa `MyOptions`, ma dwie `Option1` `Option2`właściwości i .</span><span class="sxs-lookup"><span data-stu-id="c66aa-137">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="c66aa-138">Ustawienie wartości domyślnych jest opcjonalne, ale konstruktor klas `Option1`w poniższym przykładzie ustawia wartość domyślną .</span><span class="sxs-lookup"><span data-stu-id="c66aa-138">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="c66aa-139">`Option2`ma ustawioną wartość domyślną przez zainicjowanie właściwości bezpośrednio (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-139">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="c66aa-140">Klasa `MyOptions` jest dodawana do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontenera usługi z konfiguracją i powiązana z nią:</span><span class="sxs-lookup"><span data-stu-id="c66aa-140">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="c66aa-141">Poniższy model strony używa [iniekcji zależności konstruktora,](xref:mvc/controllers/dependency-injection) <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aby uzyskać dostęp do ustawień (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-141">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="c66aa-142">Przykładowy plik *appsettings.json* określa wartości `option1` `option2`dla i:</span><span class="sxs-lookup"><span data-stu-id="c66aa-142">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="c66aa-143">Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-143">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="c66aa-144">Podczas korzystania <xref:System.Configuration.ConfigurationBuilder> z niestandardowego ładowania konfiguracji opcji z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:</span><span class="sxs-lookup"><span data-stu-id="c66aa-144">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="c66aa-145">Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>konfiguracji opcji z pliku ustawień za pośrednictwem programu .</span><span class="sxs-lookup"><span data-stu-id="c66aa-145">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="c66aa-146">Konfigurowanie prostych opcji za pomocą pełnomocnika</span><span class="sxs-lookup"><span data-stu-id="c66aa-146">Configure simple options with a delegate</span></span>

<span data-ttu-id="c66aa-147">Konfigurowanie prostych opcji z pełnomocnikiem jest pokazany jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-147">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="c66aa-148">Użyj pełnomocnika, aby ustawić wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-148">Use a delegate to set options values.</span></span> <span data-ttu-id="c66aa-149">Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy *(Models/MyOptionsWithDelegateConfig.cs):*</span><span class="sxs-lookup"><span data-stu-id="c66aa-149">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="c66aa-150">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> druga usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-150">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="c66aa-151">Używa delegata do skonfigurowania powiązania `MyOptionsWithDelegateConfig`z:</span><span class="sxs-lookup"><span data-stu-id="c66aa-151">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="c66aa-152">*Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="c66aa-152">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="c66aa-153">Można dodać wielu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-153">You can add multiple configuration providers.</span></span> <span data-ttu-id="c66aa-154">Dostawcy konfiguracji są dostępne z pakietów NuGet i są stosowane w kolejności, w jakiej są zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="c66aa-154">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="c66aa-155">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c66aa-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="c66aa-156">Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> dodaje usługę do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-156">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="c66aa-157">W poprzednim przykładzie wartości `Option1` i `Option2` są zarówno określone w *appsettings.json*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowanego delegata.</span><span class="sxs-lookup"><span data-stu-id="c66aa-157">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="c66aa-158">Gdy włączona jest więcej niż jedna usługa konfiguracjona, ostatnie źródło konfiguracji, określone, *wygrywa* i ustawia wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-158">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="c66aa-159">Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-159">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="c66aa-160">Konfiguracja podopcji</span><span class="sxs-lookup"><span data-stu-id="c66aa-160">Suboptions configuration</span></span>

<span data-ttu-id="c66aa-161">Konfiguracja podopcji jest pokazana jako przykład 3 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-161">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="c66aa-162">Aplikacje należy utworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klasy) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-162">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="c66aa-163">Części aplikacji, które wymagają wartości konfiguracji, powinny mieć dostęp tylko do wartości konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="c66aa-163">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="c66aa-164">Gdy opcje powiązania z konfiguracją, każda właściwość w typie `property[:sub-property:]`opcji jest powiązana z kluczem konfiguracji formularza .</span><span class="sxs-lookup"><span data-stu-id="c66aa-164">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="c66aa-165">Na przykład `MyOptions.Option1` właściwość jest powiązana z `Option1`kluczem `option1` , który jest odczytywany z właściwości w *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c66aa-165">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="c66aa-166">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> trzecia usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-166">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="c66aa-167">Wiąże `MySubOptions` się z `subsection` sekcją pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="c66aa-167">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="c66aa-168">Metoda `GetSection` wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obszaru nazw.</span><span class="sxs-lookup"><span data-stu-id="c66aa-168">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="c66aa-169">Przykładowy plik *appsettings.json* definiuje `subsection` członka z `suboption1` kluczami dla i: `suboption2`</span><span class="sxs-lookup"><span data-stu-id="c66aa-169">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="c66aa-170">Klasa `MySubOptions` definiuje właściwości `SubOption1` i `SubOption2`, aby pomieścić wartości opcji (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-170">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="c66aa-171">`OnGet` Metoda modelu strony zwraca ciąg z wartościami opcji (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-171">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="c66aa-172">Po uruchomieniu aplikacji `OnGet` metoda zwraca ciąg pokazujący wartości klasy suboption:</span><span class="sxs-lookup"><span data-stu-id="c66aa-172">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="c66aa-173">Opcje wtrysku</span><span class="sxs-lookup"><span data-stu-id="c66aa-173">Options injection</span></span>

<span data-ttu-id="c66aa-174">Opcje iniekcji jest pokazany jako przykład 4 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-174">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="c66aa-175">Wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="c66aa-175">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="c66aa-176">Strona Razor lub widok MVC z dyrektywą [`@inject`](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="c66aa-176">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="c66aa-177">Strona lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="c66aa-177">A page or view model.</span></span>

<span data-ttu-id="c66aa-178">Poniższy przykład z przykładowej <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplikacji wstrzykuje do modelu strony *(Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="c66aa-178">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="c66aa-179">Przykładowa aplikacja pokazuje, `IOptionsMonitor<MyOptions>` jak `@inject` wstrzyknąć z dyrektywą:</span><span class="sxs-lookup"><span data-stu-id="c66aa-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="c66aa-180">Po uruchomieniu aplikacji wartości opcji są wyświetlane na renderowanym stronie:</span><span class="sxs-lookup"><span data-stu-id="c66aa-180">When the app is run, the options values are shown in the rendered page:</span></span>

![Wartości opcji Option1: value1_from_json i Option2: -1 są ładowane z modelu i przez wstrzyknięcie do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="c66aa-182">Przeładuj dane konfiguracyjne za pomocą IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="c66aa-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="c66aa-183">Ponowne ładowanie danych <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguracyjnych za pomocą jest pokazany w przykładzie 5 w przykładzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="c66aa-184">Za <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>pomocą opcji są obliczane raz na żądanie, gdy dostęp i buforowane przez cały okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="c66aa-184">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="c66aa-185">Różnica między `IOptionsMonitor` `IOptionsSnapshot` i jest to, że:</span><span class="sxs-lookup"><span data-stu-id="c66aa-185">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="c66aa-186">`IOptionsMonitor`jest [usługą singleton,](xref:fundamentals/dependency-injection#singleton) która pobiera bieżące wartości opcji w dowolnym momencie, co jest szczególnie przydatne w zależnościach singleton.</span><span class="sxs-lookup"><span data-stu-id="c66aa-186">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="c66aa-187">`IOptionsSnapshot`jest [usługą o określonym zakresie](xref:fundamentals/dependency-injection#scoped) i udostępnia migawkę opcji w czasie konstruowania `IOptionsSnapshot<T>` obiektu.</span><span class="sxs-lookup"><span data-stu-id="c66aa-187">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="c66aa-188">Opcje migawki są przeznaczone do użytku z zależnościami przejściowymi i o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-188">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="c66aa-189">W poniższym przykładzie <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> pokazano, jak nowy jest tworzony po *appsettings.json* zmiany *(Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="c66aa-189">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="c66aa-190">Wiele żądań do serwera zwraca stałe wartości dostarczone przez plik *appsettings.json,* dopóki plik nie zostanie zmieniony i konfiguracja zostanie ponownie załadowana.</span><span class="sxs-lookup"><span data-stu-id="c66aa-190">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="c66aa-191">Na poniższej ilustracji przedstawiono wartości początkowe `option1` i `option2` wartości załadowane z pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="c66aa-191">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="c66aa-192">Zmień wartości w pliku *appsettings.json* na `value1_from_json UPDATED` i `200`.</span><span class="sxs-lookup"><span data-stu-id="c66aa-192">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="c66aa-193">Zapisz plik *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="c66aa-193">Save the *appsettings.json* file.</span></span> <span data-ttu-id="c66aa-194">Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="c66aa-194">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="c66aa-195">Obsługa nazwanych opcji za pomocą opcji IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="c66aa-195">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="c66aa-196">Nazwane opcje <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> obsługi z jest pokazany jako przykład 6 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-196">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="c66aa-197">Obsługa nazwanych opcji umożliwia aplikacji rozróżnianie nazwanych konfiguracji opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-197">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="c66aa-198">W przykładowej aplikacji nazwane opcje są deklarowane za pomocą [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), który wywołuje [ConfigureNamedOptions\<TOptions>. Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="c66aa-198">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="c66aa-199">W nazwanych opcjach rozróżniana jest wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="c66aa-199">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="c66aa-200">Przykładowa aplikacja uzyskuje dostęp <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> do nazwanych opcji za pomocą *(Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="c66aa-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="c66aa-201">Uruchamianie przykładowej aplikacji, nazwane opcje są zwracane:</span><span class="sxs-lookup"><span data-stu-id="c66aa-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="c66aa-202">`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="c66aa-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="c66aa-203">`named_options_2`wartości są dostarczane przez:</span><span class="sxs-lookup"><span data-stu-id="c66aa-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="c66aa-204">Delegat `named_options_2` w `ConfigureServices` `Option1`dla .</span><span class="sxs-lookup"><span data-stu-id="c66aa-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="c66aa-205">Wartość domyślna `Option2` dla `MyOptions` podanych przez klasę.</span><span class="sxs-lookup"><span data-stu-id="c66aa-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="c66aa-206">Konfigurowanie wszystkich opcji za pomocą metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="c66aa-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="c66aa-207">Skonfiguruj wszystkie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> opcje wystąpień za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="c66aa-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="c66aa-208">Poniższy kod `Option1` konfiguruje dla wszystkich wystąpień konfiguracji o wspólnej wartości.</span><span class="sxs-lookup"><span data-stu-id="c66aa-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="c66aa-209">Dodaj ręcznie następujący kod `Startup.ConfigureServices` do metody:</span><span class="sxs-lookup"><span data-stu-id="c66aa-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="c66aa-210">Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="c66aa-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="c66aa-211">Wszystkie opcje są nazwane wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="c66aa-211">All options are named instances.</span></span> <span data-ttu-id="c66aa-212">Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako `Options.DefaultName` kierowanie na `string.Empty`wystąpienie, czyli .</span><span class="sxs-lookup"><span data-stu-id="c66aa-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="c66aa-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>również implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="c66aa-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="c66aa-214">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> ma logiki do użycia każdego odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="c66aa-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="c66aa-215">Nazwana `null` opcja jest używana do kierowania wszystkich nazwanych wystąpień<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> zamiast <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> określonego nazwanego wystąpienia ( i użyć tej konwencji).</span><span class="sxs-lookup"><span data-stu-id="c66aa-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="c66aa-216">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="c66aa-216">OptionsBuilder API</span></span>

<span data-ttu-id="c66aa-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="c66aa-218">`OptionsBuilder`usprawnia tworzenie nazwanych opcji, ponieważ jest to `AddOptions<TOptions>(string optionsName)` tylko pojedynczy parametr początkowego wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="c66aa-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="c66aa-219">Sprawdzanie poprawności `ConfigureOptions` opcji i przeciążenia, które akceptują zależności usług są dostępne tylko za pośrednictwem `OptionsBuilder`programu .</span><span class="sxs-lookup"><span data-stu-id="c66aa-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="c66aa-220">Konfigurowanie opcji za pomocą usług DI</span><span class="sxs-lookup"><span data-stu-id="c66aa-220">Use DI services to configure options</span></span>

<span data-ttu-id="c66aa-221">Dostęp do innych usług można uzyskać z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="c66aa-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="c66aa-222">Przekaż delegata konfiguracji, aby [skonfigurować](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [opcjach\<TOptions optionsbuilder>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="c66aa-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="c66aa-223">`OptionsBuilder<TOptions>`zapewnia przeciążenia [konfiguracji,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) które umożliwiają korzystanie z maksymalnie pięciu usług w celu skonfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-223">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="c66aa-224">Utwórz własny typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> który implementuje lub rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="c66aa-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="c66aa-225">Zaleca się przekazanie delegata konfiguracji do [configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)ponieważ tworzenie usługi jest bardziej złożone.</span><span class="sxs-lookup"><span data-stu-id="c66aa-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="c66aa-226">Tworzenie własnego typu jest równoważne z tym, co robi struktura dla Ciebie podczas korzystania [z funkcji Konfiguruj](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="c66aa-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="c66aa-227">Wywołanie [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowy ogólny <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, który ma konstruktora, który akceptuje typy usług ogólnych określonych.</span><span class="sxs-lookup"><span data-stu-id="c66aa-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="c66aa-228">Sprawdzanie poprawności opcji</span><span class="sxs-lookup"><span data-stu-id="c66aa-228">Options validation</span></span>

<span data-ttu-id="c66aa-229">Sprawdzanie poprawności opcji umożliwia sprawdzanie poprawności opcji podczas konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="c66aa-230">Wywołanie `Validate` z metody `true` sprawdzania poprawności, `false` która zwraca, jeśli opcje są prawidłowe i jeśli nie są prawidłowe:</span><span class="sxs-lookup"><span data-stu-id="c66aa-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c66aa-231">W poprzednim przykładzie ustawia nazwę `optionalOptionsName`opcji wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="c66aa-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="c66aa-232">Domyślnym wystąpieniem `Options.DefaultName`opcji jest .</span><span class="sxs-lookup"><span data-stu-id="c66aa-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="c66aa-233">Sprawdzanie poprawności jest uruchamiane podczas tworzenia wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="c66aa-234">Wystąpienie opcji jest gwarantowane do przekazania sprawdzania poprawności przy pierwszym dostępie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-234">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c66aa-235">Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-235">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="c66aa-236">Na przykład `IOptionsSnapshot` opcje są tworzone i sprawdzane raz na żądanie, gdy opcje są po raz pierwszy dostępne.</span><span class="sxs-lookup"><span data-stu-id="c66aa-236">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="c66aa-237">Opcje `IOptionsSnapshot` nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania*.</span><span class="sxs-lookup"><span data-stu-id="c66aa-237">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="c66aa-238">Metoda `Validate` akceptuje . `Func<TOptions, bool>`</span><span class="sxs-lookup"><span data-stu-id="c66aa-238">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="c66aa-239">Aby w pełni dostosować `IValidateOptions<TOptions>`walidację, zaimplementuj, co pozwala na:</span><span class="sxs-lookup"><span data-stu-id="c66aa-239">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="c66aa-240">Sprawdzanie poprawności wielu typów opcji:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="c66aa-240">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="c66aa-241">Sprawdzanie poprawności, które zależy od innego typu opcji:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="c66aa-241">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="c66aa-242">`IValidateOptions`Sprawdza poprawność:</span><span class="sxs-lookup"><span data-stu-id="c66aa-242">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="c66aa-243">Konkretne wystąpienie opcji o nazwie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-243">A specific named options instance.</span></span>
* <span data-ttu-id="c66aa-244">Wszystkie opcje, gdy `name` jest `null`.</span><span class="sxs-lookup"><span data-stu-id="c66aa-244">All options when `name` is `null`.</span></span>

<span data-ttu-id="c66aa-245">Powrót `ValidateOptionsResult` z implementacji interfejsu:</span><span class="sxs-lookup"><span data-stu-id="c66aa-245">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="c66aa-246">Sprawdzanie poprawności oparte na adnotacji danych jest dostępne w pakiecie [Microsoft.Extensions.Options.DataAnnotations,](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) wywołując <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metodę na `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="c66aa-246">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="c66aa-247">`Microsoft.Extensions.Options.DataAnnotations`niejawnie odwołuje się w ASP.NET aplikacji Core.</span><span class="sxs-lookup"><span data-stu-id="c66aa-247">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="c66aa-248">Gorliwa weryfikacja (nie szybko przy starcie) jest rozważana dla przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-248">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="c66aa-249">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="c66aa-249">Options post-configuration</span></span>

<span data-ttu-id="c66aa-250">Ustaw konfigurację <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>postkonfiguracj za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="c66aa-250">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="c66aa-251">Po zakończeniu konfiguracji <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uruchamia się po zakończeniu całej konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-251">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="c66aa-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępna do konfiguracji po nazwie opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="c66aa-253">Służy <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> do konfigurowania po skonfigurowaniu wszystkich wystąpień konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="c66aa-253">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="c66aa-254">Uzyskiwanie dostępu do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="c66aa-254">Accessing options during startup</span></span>

<span data-ttu-id="c66aa-255"><xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> może być `Startup.Configure`używany w , `Configure` ponieważ usługi są budowane przed wykonaniem metody.</span><span class="sxs-lookup"><span data-stu-id="c66aa-255"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="c66aa-256">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ani `Startup.ConfigureServices`nie używaj w pliku .</span><span class="sxs-lookup"><span data-stu-id="c66aa-256">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c66aa-257">Niespójne stan opcji może istnieć ze względu na kolejność rejestracji usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-257">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c66aa-258">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-258">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="c66aa-259">Gdy [ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza do oddzielnych klas, aplikacja jest zgodna z dwiema ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="c66aa-259">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="c66aa-260">[Zasada segregacji interfejsu (ISP) lub](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scenariusze hermetyzacji (klasy), które zależą od ustawień konfiguracji, zależą tylko od używanych ustawień konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-260">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="c66aa-261">[Separacja dotyczy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Ustawienia dla różnych części aplikacji nie są zależne lub powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="c66aa-261">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="c66aa-262">Opcje zapewniają również mechanizm sprawdzania poprawności danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="c66aa-262">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="c66aa-263">Aby uzyskać więcej informacji, zobacz sekcję [Sprawdzanie poprawności opcji.](#options-validation)</span><span class="sxs-lookup"><span data-stu-id="c66aa-263">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="c66aa-264">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c66aa-264">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c66aa-265">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="c66aa-265">Prerequisites</span></span>

<span data-ttu-id="c66aa-266">Odwołuje się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) lub dodaj odwołanie do pakietu [Microsoft.Extensions.Options.ConfigurationExtensions.](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)</span><span class="sxs-lookup"><span data-stu-id="c66aa-266">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="c66aa-267">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="c66aa-267">Options interfaces</span></span>

<span data-ttu-id="c66aa-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania `TOptions` opcjami powiadomień o wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="c66aa-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="c66aa-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="c66aa-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="c66aa-270">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="c66aa-270">Change notifications</span></span>
* [<span data-ttu-id="c66aa-271">Opcje nazwane</span><span class="sxs-lookup"><span data-stu-id="c66aa-271">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="c66aa-272">Konfiguracja z doładowaniem</span><span class="sxs-lookup"><span data-stu-id="c66aa-272">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="c66aa-273">Selektywne unieważnienie<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>opcji ( )</span><span class="sxs-lookup"><span data-stu-id="c66aa-273">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="c66aa-274">[Scenariusze po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę <xref:Microsoft.Extensions.Options.IConfigureOptions%601> opcji po zakończeniu całej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-274">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="c66aa-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych opcji wystąpień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="c66aa-276">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="c66aa-276">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="c66aa-277">Domyślna implementacja <xref:Microsoft.Extensions.Options.IConfigureOptions%601> przyjmuje <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> wszystkie zarejestrowane i uruchamia wszystkie konfiguracje pierwszy, a następnie po konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-277">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="c66aa-278">Rozróżnia <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="c66aa-278">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="c66aa-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> przez `TOptions` do buforowania wystąpień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="c66aa-280">Unieważnia <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> opcje wystąpień na monitorze, tak aby wartość<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>została ponownie skompensowana ( ).</span><span class="sxs-lookup"><span data-stu-id="c66aa-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="c66aa-281">Wartości można wprowadzać <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>ręcznie za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="c66aa-281">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="c66aa-282">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> jest używana, gdy wszystkie nazwane wystąpienia powinny być odtworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-282">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="c66aa-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie skomputowane na każde żądanie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="c66aa-284">Aby uzyskać więcej informacji, zobacz [reload danych konfiguracyjnych z IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) sekcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-284">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="c66aa-285"><xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-285"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="c66aa-286">Jednak <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje poprzednich scenariuszy . <xref:Microsoft.Extensions.Options.IOptionsMonitor%601></span><span class="sxs-lookup"><span data-stu-id="c66aa-286">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="c66aa-287">Możesz nadal używać <xref:Microsoft.Extensions.Options.IOptions%601> w istniejących strukturach i bibliotekach, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>interfejsu i nie wymagają scenariuszy dostarczonych przez program .</span><span class="sxs-lookup"><span data-stu-id="c66aa-287">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="c66aa-288">Konfiguracja opcji ogólnych</span><span class="sxs-lookup"><span data-stu-id="c66aa-288">General options configuration</span></span>

<span data-ttu-id="c66aa-289">Konfiguracja opcji ogólnych jest pokazana jako przykład 1 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-289">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="c66aa-290">Klasa opcji musi być nie abstrakcyjna z konstruktorem bez parametrów publicznych.</span><span class="sxs-lookup"><span data-stu-id="c66aa-290">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="c66aa-291">Następująca klasa `MyOptions`, ma dwie `Option1` `Option2`właściwości i .</span><span class="sxs-lookup"><span data-stu-id="c66aa-291">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="c66aa-292">Ustawienie wartości domyślnych jest opcjonalne, ale konstruktor klas `Option1`w poniższym przykładzie ustawia wartość domyślną .</span><span class="sxs-lookup"><span data-stu-id="c66aa-292">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="c66aa-293">`Option2`ma ustawioną wartość domyślną przez zainicjowanie właściwości bezpośrednio (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-293">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="c66aa-294">Klasa `MyOptions` jest dodawana do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontenera usługi z konfiguracją i powiązana z nią:</span><span class="sxs-lookup"><span data-stu-id="c66aa-294">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="c66aa-295">Poniższy model strony używa [iniekcji zależności konstruktora,](xref:mvc/controllers/dependency-injection) <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aby uzyskać dostęp do ustawień (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-295">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="c66aa-296">Przykładowy plik *appsettings.json* określa wartości `option1` `option2`dla i:</span><span class="sxs-lookup"><span data-stu-id="c66aa-296">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="c66aa-297">Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-297">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="c66aa-298">Podczas korzystania <xref:System.Configuration.ConfigurationBuilder> z niestandardowego ładowania konfiguracji opcji z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:</span><span class="sxs-lookup"><span data-stu-id="c66aa-298">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="c66aa-299">Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>konfiguracji opcji z pliku ustawień za pośrednictwem programu .</span><span class="sxs-lookup"><span data-stu-id="c66aa-299">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="c66aa-300">Konfigurowanie prostych opcji za pomocą pełnomocnika</span><span class="sxs-lookup"><span data-stu-id="c66aa-300">Configure simple options with a delegate</span></span>

<span data-ttu-id="c66aa-301">Konfigurowanie prostych opcji z pełnomocnikiem jest pokazany jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-301">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="c66aa-302">Użyj pełnomocnika, aby ustawić wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-302">Use a delegate to set options values.</span></span> <span data-ttu-id="c66aa-303">Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy *(Models/MyOptionsWithDelegateConfig.cs):*</span><span class="sxs-lookup"><span data-stu-id="c66aa-303">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="c66aa-304">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> druga usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-304">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="c66aa-305">Używa delegata do skonfigurowania powiązania `MyOptionsWithDelegateConfig`z:</span><span class="sxs-lookup"><span data-stu-id="c66aa-305">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="c66aa-306">*Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="c66aa-306">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="c66aa-307">Można dodać wielu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-307">You can add multiple configuration providers.</span></span> <span data-ttu-id="c66aa-308">Dostawcy konfiguracji są dostępne z pakietów NuGet i są stosowane w kolejności, w jakiej są zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="c66aa-308">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="c66aa-309">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c66aa-309">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="c66aa-310">Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> dodaje usługę do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-310">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="c66aa-311">W poprzednim przykładzie wartości `Option1` i `Option2` są zarówno określone w *appsettings.json*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowanego delegata.</span><span class="sxs-lookup"><span data-stu-id="c66aa-311">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="c66aa-312">Gdy włączona jest więcej niż jedna usługa konfiguracjona, ostatnie źródło konfiguracji, określone, *wygrywa* i ustawia wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-312">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="c66aa-313">Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-313">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="c66aa-314">Konfiguracja podopcji</span><span class="sxs-lookup"><span data-stu-id="c66aa-314">Suboptions configuration</span></span>

<span data-ttu-id="c66aa-315">Konfiguracja podopcji jest pokazana jako przykład 3 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-315">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="c66aa-316">Aplikacje należy utworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klasy) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-316">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="c66aa-317">Części aplikacji, które wymagają wartości konfiguracji, powinny mieć dostęp tylko do wartości konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="c66aa-317">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="c66aa-318">Gdy opcje powiązania z konfiguracją, każda właściwość w typie `property[:sub-property:]`opcji jest powiązana z kluczem konfiguracji formularza .</span><span class="sxs-lookup"><span data-stu-id="c66aa-318">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="c66aa-319">Na przykład `MyOptions.Option1` właściwość jest powiązana z `Option1`kluczem `option1` , który jest odczytywany z właściwości w *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c66aa-319">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="c66aa-320">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> trzecia usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-320">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="c66aa-321">Wiąże `MySubOptions` się z `subsection` sekcją pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="c66aa-321">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="c66aa-322">Metoda `GetSection` wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obszaru nazw.</span><span class="sxs-lookup"><span data-stu-id="c66aa-322">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="c66aa-323">Przykładowy plik *appsettings.json* definiuje `subsection` członka z `suboption1` kluczami dla i: `suboption2`</span><span class="sxs-lookup"><span data-stu-id="c66aa-323">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="c66aa-324">Klasa `MySubOptions` definiuje właściwości `SubOption1` i `SubOption2`, aby pomieścić wartości opcji (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-324">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="c66aa-325">`OnGet` Metoda modelu strony zwraca ciąg z wartościami opcji (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-325">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="c66aa-326">Po uruchomieniu aplikacji `OnGet` metoda zwraca ciąg pokazujący wartości klasy suboption:</span><span class="sxs-lookup"><span data-stu-id="c66aa-326">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="c66aa-327">Opcje wtrysku</span><span class="sxs-lookup"><span data-stu-id="c66aa-327">Options injection</span></span>

<span data-ttu-id="c66aa-328">Opcje iniekcji jest pokazany jako przykład 4 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-328">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="c66aa-329">Wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="c66aa-329">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="c66aa-330">Strona Razor lub widok MVC z dyrektywą [`@inject`](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="c66aa-330">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="c66aa-331">Strona lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="c66aa-331">A page or view model.</span></span>

<span data-ttu-id="c66aa-332">Poniższy przykład z przykładowej <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplikacji wstrzykuje do modelu strony *(Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="c66aa-332">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="c66aa-333">Przykładowa aplikacja pokazuje, `IOptionsMonitor<MyOptions>` jak `@inject` wstrzyknąć z dyrektywą:</span><span class="sxs-lookup"><span data-stu-id="c66aa-333">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="c66aa-334">Po uruchomieniu aplikacji wartości opcji są wyświetlane na renderowanym stronie:</span><span class="sxs-lookup"><span data-stu-id="c66aa-334">When the app is run, the options values are shown in the rendered page:</span></span>

![Wartości opcji Option1: value1_from_json i Option2: -1 są ładowane z modelu i przez wstrzyknięcie do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="c66aa-336">Przeładuj dane konfiguracyjne za pomocą IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="c66aa-336">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="c66aa-337">Ponowne ładowanie danych <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguracyjnych za pomocą jest pokazany w przykładzie 5 w przykładzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-337">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="c66aa-338">Za <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>pomocą opcji są obliczane raz na żądanie, gdy dostęp i buforowane przez cały okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="c66aa-338">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="c66aa-339">Różnica między `IOptionsMonitor` `IOptionsSnapshot` i jest to, że:</span><span class="sxs-lookup"><span data-stu-id="c66aa-339">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="c66aa-340">`IOptionsMonitor`jest [usługą singleton,](xref:fundamentals/dependency-injection#singleton) która pobiera bieżące wartości opcji w dowolnym momencie, co jest szczególnie przydatne w zależnościach singleton.</span><span class="sxs-lookup"><span data-stu-id="c66aa-340">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="c66aa-341">`IOptionsSnapshot`jest [usługą o określonym zakresie](xref:fundamentals/dependency-injection#scoped) i udostępnia migawkę opcji w czasie konstruowania `IOptionsSnapshot<T>` obiektu.</span><span class="sxs-lookup"><span data-stu-id="c66aa-341">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="c66aa-342">Opcje migawki są przeznaczone do użytku z zależnościami przejściowymi i o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-342">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="c66aa-343">W poniższym przykładzie <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> pokazano, jak nowy jest tworzony po *appsettings.json* zmiany *(Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="c66aa-343">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="c66aa-344">Wiele żądań do serwera zwraca stałe wartości dostarczone przez plik *appsettings.json,* dopóki plik nie zostanie zmieniony i konfiguracja zostanie ponownie załadowana.</span><span class="sxs-lookup"><span data-stu-id="c66aa-344">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="c66aa-345">Na poniższej ilustracji przedstawiono wartości początkowe `option1` i `option2` wartości załadowane z pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="c66aa-345">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="c66aa-346">Zmień wartości w pliku *appsettings.json* na `value1_from_json UPDATED` i `200`.</span><span class="sxs-lookup"><span data-stu-id="c66aa-346">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="c66aa-347">Zapisz plik *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="c66aa-347">Save the *appsettings.json* file.</span></span> <span data-ttu-id="c66aa-348">Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="c66aa-348">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="c66aa-349">Obsługa nazwanych opcji za pomocą opcji IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="c66aa-349">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="c66aa-350">Nazwane opcje <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> obsługi z jest pokazany jako przykład 6 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-350">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="c66aa-351">Obsługa nazwanych opcji umożliwia aplikacji rozróżnianie nazwanych konfiguracji opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-351">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="c66aa-352">W przykładowej aplikacji nazwane opcje są deklarowane za pomocą [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), który wywołuje [ConfigureNamedOptions\<TOptions>. Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="c66aa-352">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="c66aa-353">W nazwanych opcjach rozróżniana jest wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="c66aa-353">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="c66aa-354">Przykładowa aplikacja uzyskuje dostęp <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> do nazwanych opcji za pomocą *(Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="c66aa-354">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="c66aa-355">Uruchamianie przykładowej aplikacji, nazwane opcje są zwracane:</span><span class="sxs-lookup"><span data-stu-id="c66aa-355">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="c66aa-356">`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="c66aa-356">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="c66aa-357">`named_options_2`wartości są dostarczane przez:</span><span class="sxs-lookup"><span data-stu-id="c66aa-357">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="c66aa-358">Delegat `named_options_2` w `ConfigureServices` `Option1`dla .</span><span class="sxs-lookup"><span data-stu-id="c66aa-358">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="c66aa-359">Wartość domyślna `Option2` dla `MyOptions` podanych przez klasę.</span><span class="sxs-lookup"><span data-stu-id="c66aa-359">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="c66aa-360">Konfigurowanie wszystkich opcji za pomocą metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="c66aa-360">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="c66aa-361">Skonfiguruj wszystkie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> opcje wystąpień za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="c66aa-361">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="c66aa-362">Poniższy kod `Option1` konfiguruje dla wszystkich wystąpień konfiguracji o wspólnej wartości.</span><span class="sxs-lookup"><span data-stu-id="c66aa-362">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="c66aa-363">Dodaj ręcznie następujący kod `Startup.ConfigureServices` do metody:</span><span class="sxs-lookup"><span data-stu-id="c66aa-363">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="c66aa-364">Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="c66aa-364">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="c66aa-365">Wszystkie opcje są nazwane wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="c66aa-365">All options are named instances.</span></span> <span data-ttu-id="c66aa-366">Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako `Options.DefaultName` kierowanie na `string.Empty`wystąpienie, czyli .</span><span class="sxs-lookup"><span data-stu-id="c66aa-366">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="c66aa-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>również implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="c66aa-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="c66aa-368">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> ma logiki do użycia każdego odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="c66aa-368">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="c66aa-369">Nazwana `null` opcja jest używana do kierowania wszystkich nazwanych wystąpień<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> zamiast <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> określonego nazwanego wystąpienia ( i użyć tej konwencji).</span><span class="sxs-lookup"><span data-stu-id="c66aa-369">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="c66aa-370">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="c66aa-370">OptionsBuilder API</span></span>

<span data-ttu-id="c66aa-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="c66aa-372">`OptionsBuilder`usprawnia tworzenie nazwanych opcji, ponieważ jest to `AddOptions<TOptions>(string optionsName)` tylko pojedynczy parametr początkowego wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="c66aa-372">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="c66aa-373">Sprawdzanie poprawności `ConfigureOptions` opcji i przeciążenia, które akceptują zależności usług są dostępne tylko za pośrednictwem `OptionsBuilder`programu .</span><span class="sxs-lookup"><span data-stu-id="c66aa-373">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="c66aa-374">Konfigurowanie opcji za pomocą usług DI</span><span class="sxs-lookup"><span data-stu-id="c66aa-374">Use DI services to configure options</span></span>

<span data-ttu-id="c66aa-375">Dostęp do innych usług można uzyskać z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="c66aa-375">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="c66aa-376">Przekaż delegata konfiguracji, aby [skonfigurować](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [opcjach\<TOptions optionsbuilder>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="c66aa-376">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="c66aa-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [Configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) które umożliwiają korzystanie z maksymalnie pięciu usług w celu skonfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="c66aa-378">Utwórz własny typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> który implementuje lub rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="c66aa-378">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="c66aa-379">Zaleca się przekazanie delegata konfiguracji do [configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)ponieważ tworzenie usługi jest bardziej złożone.</span><span class="sxs-lookup"><span data-stu-id="c66aa-379">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="c66aa-380">Tworzenie własnego typu jest równoważne z tym, co robi struktura dla Ciebie podczas korzystania [z funkcji Konfiguruj](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="c66aa-380">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="c66aa-381">Wywołanie [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowy ogólny <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, który ma konstruktora, który akceptuje typy usług ogólnych określonych.</span><span class="sxs-lookup"><span data-stu-id="c66aa-381">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="c66aa-382">Sprawdzanie poprawności opcji</span><span class="sxs-lookup"><span data-stu-id="c66aa-382">Options validation</span></span>

<span data-ttu-id="c66aa-383">Sprawdzanie poprawności opcji umożliwia sprawdzanie poprawności opcji podczas konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-383">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="c66aa-384">Wywołanie `Validate` z metody `true` sprawdzania poprawności, `false` która zwraca, jeśli opcje są prawidłowe i jeśli nie są prawidłowe:</span><span class="sxs-lookup"><span data-stu-id="c66aa-384">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="c66aa-385">W poprzednim przykładzie ustawia nazwę `optionalOptionsName`opcji wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="c66aa-385">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="c66aa-386">Domyślnym wystąpieniem `Options.DefaultName`opcji jest .</span><span class="sxs-lookup"><span data-stu-id="c66aa-386">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="c66aa-387">Sprawdzanie poprawności jest uruchamiane podczas tworzenia wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-387">Validation runs when the options instance is created.</span></span> <span data-ttu-id="c66aa-388">Wystąpienie opcji jest gwarantowane do przekazania sprawdzania poprawności przy pierwszym dostępie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-388">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c66aa-389">Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-389">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="c66aa-390">Na przykład `IOptionsSnapshot` opcje są tworzone i sprawdzane raz na żądanie, gdy opcje są po raz pierwszy dostępne.</span><span class="sxs-lookup"><span data-stu-id="c66aa-390">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="c66aa-391">Opcje `IOptionsSnapshot` nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania*.</span><span class="sxs-lookup"><span data-stu-id="c66aa-391">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="c66aa-392">Metoda `Validate` akceptuje . `Func<TOptions, bool>`</span><span class="sxs-lookup"><span data-stu-id="c66aa-392">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="c66aa-393">Aby w pełni dostosować `IValidateOptions<TOptions>`walidację, zaimplementuj, co pozwala na:</span><span class="sxs-lookup"><span data-stu-id="c66aa-393">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="c66aa-394">Sprawdzanie poprawności wielu typów opcji:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="c66aa-394">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="c66aa-395">Sprawdzanie poprawności, które zależy od innego typu opcji:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="c66aa-395">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="c66aa-396">`IValidateOptions`Sprawdza poprawność:</span><span class="sxs-lookup"><span data-stu-id="c66aa-396">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="c66aa-397">Konkretne wystąpienie opcji o nazwie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-397">A specific named options instance.</span></span>
* <span data-ttu-id="c66aa-398">Wszystkie opcje, gdy `name` jest `null`.</span><span class="sxs-lookup"><span data-stu-id="c66aa-398">All options when `name` is `null`.</span></span>

<span data-ttu-id="c66aa-399">Powrót `ValidateOptionsResult` z implementacji interfejsu:</span><span class="sxs-lookup"><span data-stu-id="c66aa-399">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="c66aa-400">Sprawdzanie poprawności oparte na adnotacji danych jest dostępne w pakiecie [Microsoft.Extensions.Options.DataAnnotations,](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) wywołując <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metodę na `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="c66aa-400">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="c66aa-401">`Microsoft.Extensions.Options.DataAnnotations`znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c66aa-401">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="c66aa-402">Gorliwa weryfikacja (nie szybko przy starcie) jest rozważana dla przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-402">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="c66aa-403">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="c66aa-403">Options post-configuration</span></span>

<span data-ttu-id="c66aa-404">Ustaw konfigurację <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>postkonfiguracj za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="c66aa-404">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="c66aa-405">Po zakończeniu konfiguracji <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uruchamia się po zakończeniu całej konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-405">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="c66aa-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępna do konfiguracji po nazwie opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="c66aa-407">Służy <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> do konfigurowania po skonfigurowaniu wszystkich wystąpień konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="c66aa-407">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="c66aa-408">Uzyskiwanie dostępu do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="c66aa-408">Accessing options during startup</span></span>

<span data-ttu-id="c66aa-409"><xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> może być `Startup.Configure`używany w , `Configure` ponieważ usługi są budowane przed wykonaniem metody.</span><span class="sxs-lookup"><span data-stu-id="c66aa-409"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="c66aa-410">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ani `Startup.ConfigureServices`nie używaj w pliku .</span><span class="sxs-lookup"><span data-stu-id="c66aa-410">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c66aa-411">Niespójne stan opcji może istnieć ze względu na kolejność rejestracji usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-411">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c66aa-412">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-412">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="c66aa-413">Gdy [ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza do oddzielnych klas, aplikacja jest zgodna z dwiema ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="c66aa-413">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="c66aa-414">[Zasada segregacji interfejsu (ISP) lub](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scenariusze hermetyzacji (klasy), które zależą od ustawień konfiguracji, zależą tylko od używanych ustawień konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-414">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="c66aa-415">[Separacja dotyczy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Ustawienia dla różnych części aplikacji nie są zależne lub powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="c66aa-415">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="c66aa-416">Opcje zapewniają również mechanizm sprawdzania poprawności danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="c66aa-416">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="c66aa-417">Aby uzyskać więcej informacji, zobacz sekcję [Sprawdzanie poprawności opcji.](#options-validation)</span><span class="sxs-lookup"><span data-stu-id="c66aa-417">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="c66aa-418">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c66aa-418">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c66aa-419">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="c66aa-419">Prerequisites</span></span>

<span data-ttu-id="c66aa-420">Odwołuje się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) lub dodaj odwołanie do pakietu [Microsoft.Extensions.Options.ConfigurationExtensions.](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)</span><span class="sxs-lookup"><span data-stu-id="c66aa-420">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="c66aa-421">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="c66aa-421">Options interfaces</span></span>

<span data-ttu-id="c66aa-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania `TOptions` opcjami powiadomień o wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="c66aa-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="c66aa-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="c66aa-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="c66aa-424">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="c66aa-424">Change notifications</span></span>
* [<span data-ttu-id="c66aa-425">Opcje nazwane</span><span class="sxs-lookup"><span data-stu-id="c66aa-425">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="c66aa-426">Konfiguracja z doładowaniem</span><span class="sxs-lookup"><span data-stu-id="c66aa-426">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="c66aa-427">Selektywne unieważnienie<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>opcji ( )</span><span class="sxs-lookup"><span data-stu-id="c66aa-427">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="c66aa-428">[Scenariusze po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę <xref:Microsoft.Extensions.Options.IConfigureOptions%601> opcji po zakończeniu całej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-428">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="c66aa-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych opcji wystąpień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="c66aa-430">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="c66aa-430">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="c66aa-431">Domyślna implementacja <xref:Microsoft.Extensions.Options.IConfigureOptions%601> przyjmuje <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> wszystkie zarejestrowane i uruchamia wszystkie konfiguracje pierwszy, a następnie po konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-431">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="c66aa-432">Rozróżnia <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="c66aa-432">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="c66aa-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> przez `TOptions` do buforowania wystąpień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="c66aa-434">Unieważnia <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> opcje wystąpień na monitorze, tak aby wartość<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>została ponownie skompensowana ( ).</span><span class="sxs-lookup"><span data-stu-id="c66aa-434">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="c66aa-435">Wartości można wprowadzać <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>ręcznie za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="c66aa-435">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="c66aa-436">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> jest używana, gdy wszystkie nazwane wystąpienia powinny być odtworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-436">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="c66aa-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie skomputowane na każde żądanie.</span><span class="sxs-lookup"><span data-stu-id="c66aa-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="c66aa-438">Aby uzyskać więcej informacji, zobacz [reload danych konfiguracyjnych z IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) sekcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-438">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="c66aa-439"><xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-439"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="c66aa-440">Jednak <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje poprzednich scenariuszy . <xref:Microsoft.Extensions.Options.IOptionsMonitor%601></span><span class="sxs-lookup"><span data-stu-id="c66aa-440">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="c66aa-441">Możesz nadal używać <xref:Microsoft.Extensions.Options.IOptions%601> w istniejących strukturach i bibliotekach, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>interfejsu i nie wymagają scenariuszy dostarczonych przez program .</span><span class="sxs-lookup"><span data-stu-id="c66aa-441">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="c66aa-442">Konfiguracja opcji ogólnych</span><span class="sxs-lookup"><span data-stu-id="c66aa-442">General options configuration</span></span>

<span data-ttu-id="c66aa-443">Konfiguracja opcji ogólnych jest pokazana jako przykład 1 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-443">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="c66aa-444">Klasa opcji musi być nie abstrakcyjna z konstruktorem bez parametrów publicznych.</span><span class="sxs-lookup"><span data-stu-id="c66aa-444">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="c66aa-445">Następująca klasa `MyOptions`, ma dwie `Option1` `Option2`właściwości i .</span><span class="sxs-lookup"><span data-stu-id="c66aa-445">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="c66aa-446">Ustawienie wartości domyślnych jest opcjonalne, ale konstruktor klas `Option1`w poniższym przykładzie ustawia wartość domyślną .</span><span class="sxs-lookup"><span data-stu-id="c66aa-446">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="c66aa-447">`Option2`ma ustawioną wartość domyślną przez zainicjowanie właściwości bezpośrednio (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-447">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="c66aa-448">Klasa `MyOptions` jest dodawana do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontenera usługi z konfiguracją i powiązana z nią:</span><span class="sxs-lookup"><span data-stu-id="c66aa-448">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="c66aa-449">Poniższy model strony używa [iniekcji zależności konstruktora,](xref:mvc/controllers/dependency-injection) <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aby uzyskać dostęp do ustawień (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-449">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="c66aa-450">Przykładowy plik *appsettings.json* określa wartości `option1` `option2`dla i:</span><span class="sxs-lookup"><span data-stu-id="c66aa-450">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="c66aa-451">Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-451">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="c66aa-452">Podczas korzystania <xref:System.Configuration.ConfigurationBuilder> z niestandardowego ładowania konfiguracji opcji z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:</span><span class="sxs-lookup"><span data-stu-id="c66aa-452">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="c66aa-453">Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>konfiguracji opcji z pliku ustawień za pośrednictwem programu .</span><span class="sxs-lookup"><span data-stu-id="c66aa-453">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="c66aa-454">Konfigurowanie prostych opcji za pomocą pełnomocnika</span><span class="sxs-lookup"><span data-stu-id="c66aa-454">Configure simple options with a delegate</span></span>

<span data-ttu-id="c66aa-455">Konfigurowanie prostych opcji z pełnomocnikiem jest pokazany jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-455">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="c66aa-456">Użyj pełnomocnika, aby ustawić wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-456">Use a delegate to set options values.</span></span> <span data-ttu-id="c66aa-457">Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy *(Models/MyOptionsWithDelegateConfig.cs):*</span><span class="sxs-lookup"><span data-stu-id="c66aa-457">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="c66aa-458">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> druga usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-458">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="c66aa-459">Używa delegata do skonfigurowania powiązania `MyOptionsWithDelegateConfig`z:</span><span class="sxs-lookup"><span data-stu-id="c66aa-459">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="c66aa-460">*Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="c66aa-460">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="c66aa-461">Można dodać wielu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-461">You can add multiple configuration providers.</span></span> <span data-ttu-id="c66aa-462">Dostawcy konfiguracji są dostępne z pakietów NuGet i są stosowane w kolejności, w jakiej są zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="c66aa-462">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="c66aa-463">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c66aa-463">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="c66aa-464">Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> dodaje usługę do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-464">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="c66aa-465">W poprzednim przykładzie wartości `Option1` i `Option2` są zarówno określone w *appsettings.json*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowanego delegata.</span><span class="sxs-lookup"><span data-stu-id="c66aa-465">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="c66aa-466">Gdy włączona jest więcej niż jedna usługa konfiguracjona, ostatnie źródło konfiguracji, określone, *wygrywa* i ustawia wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-466">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="c66aa-467">Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-467">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="c66aa-468">Konfiguracja podopcji</span><span class="sxs-lookup"><span data-stu-id="c66aa-468">Suboptions configuration</span></span>

<span data-ttu-id="c66aa-469">Konfiguracja podopcji jest pokazana jako przykład 3 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-469">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="c66aa-470">Aplikacje należy utworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klasy) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-470">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="c66aa-471">Części aplikacji, które wymagają wartości konfiguracji, powinny mieć dostęp tylko do wartości konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="c66aa-471">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="c66aa-472">Gdy opcje powiązania z konfiguracją, każda właściwość w typie `property[:sub-property:]`opcji jest powiązana z kluczem konfiguracji formularza .</span><span class="sxs-lookup"><span data-stu-id="c66aa-472">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="c66aa-473">Na przykład `MyOptions.Option1` właściwość jest powiązana z `Option1`kluczem `option1` , który jest odczytywany z właściwości w *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c66aa-473">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="c66aa-474">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> trzecia usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-474">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="c66aa-475">Wiąże `MySubOptions` się z `subsection` sekcją pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="c66aa-475">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="c66aa-476">Metoda `GetSection` wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obszaru nazw.</span><span class="sxs-lookup"><span data-stu-id="c66aa-476">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="c66aa-477">Przykładowy plik *appsettings.json* definiuje `subsection` członka z `suboption1` kluczami dla i: `suboption2`</span><span class="sxs-lookup"><span data-stu-id="c66aa-477">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="c66aa-478">Klasa `MySubOptions` definiuje właściwości `SubOption1` i `SubOption2`, aby pomieścić wartości opcji (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-478">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="c66aa-479">`OnGet` Metoda modelu strony zwraca ciąg z wartościami opcji (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-479">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="c66aa-480">Po uruchomieniu aplikacji `OnGet` metoda zwraca ciąg pokazujący wartości klasy suboption:</span><span class="sxs-lookup"><span data-stu-id="c66aa-480">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="c66aa-481">Opcje dostępne w modelu widoku lub z bezpośrednim wtryskiem widoku</span><span class="sxs-lookup"><span data-stu-id="c66aa-481">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="c66aa-482">Opcje dostępne w modelu widoku lub z bezpośrednim iniekcją widoku są przedstawione jako przykład 4 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-482">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="c66aa-483">Opcje mogą być dostarczane w modelu <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> widoku lub wstrzykując bezpośrednio do widoku (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="c66aa-483">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="c66aa-484">Przykładowa aplikacja pokazuje, `IOptionsMonitor<MyOptions>` jak `@inject` wstrzyknąć z dyrektywą:</span><span class="sxs-lookup"><span data-stu-id="c66aa-484">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="c66aa-485">Po uruchomieniu aplikacji wartości opcji są wyświetlane na renderowanym stronie:</span><span class="sxs-lookup"><span data-stu-id="c66aa-485">When the app is run, the options values are shown in the rendered page:</span></span>

![Wartości opcji Option1: value1_from_json i Option2: -1 są ładowane z modelu i przez wstrzyknięcie do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="c66aa-487">Przeładuj dane konfiguracyjne za pomocą IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="c66aa-487">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="c66aa-488">Ponowne ładowanie danych <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguracyjnych za pomocą jest pokazany w przykładzie 5 w przykładzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-488">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="c66aa-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>obsługuje opcje przeładowywania przy minimalnym obciążeniu przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="c66aa-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="c66aa-490">Opcje są obliczane raz na żądanie, gdy dostęp i buforowane przez cały okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="c66aa-490">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="c66aa-491">W poniższym przykładzie <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> pokazano, jak nowy jest tworzony po *appsettings.json* zmiany *(Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="c66aa-491">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="c66aa-492">Wiele żądań do serwera zwraca stałe wartości dostarczone przez plik *appsettings.json,* dopóki plik nie zostanie zmieniony i konfiguracja zostanie ponownie załadowana.</span><span class="sxs-lookup"><span data-stu-id="c66aa-492">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="c66aa-493">Na poniższej ilustracji przedstawiono wartości początkowe `option1` i `option2` wartości załadowane z pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="c66aa-493">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="c66aa-494">Zmień wartości w pliku *appsettings.json* na `value1_from_json UPDATED` i `200`.</span><span class="sxs-lookup"><span data-stu-id="c66aa-494">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="c66aa-495">Zapisz plik *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="c66aa-495">Save the *appsettings.json* file.</span></span> <span data-ttu-id="c66aa-496">Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="c66aa-496">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="c66aa-497">Obsługa nazwanych opcji za pomocą opcji IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="c66aa-497">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="c66aa-498">Nazwane opcje <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> obsługi z jest pokazany jako przykład 6 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-498">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="c66aa-499">Obsługa nazwanych opcji umożliwia aplikacji rozróżnianie nazwanych konfiguracji opcji.</span><span class="sxs-lookup"><span data-stu-id="c66aa-499">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="c66aa-500">W przykładowej aplikacji nazwane opcje są deklarowane za pomocą [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), który wywołuje [ConfigureNamedOptions\<TOptions>. Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="c66aa-500">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="c66aa-501">W nazwanych opcjach rozróżniana jest wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="c66aa-501">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="c66aa-502">Przykładowa aplikacja uzyskuje dostęp <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> do nazwanych opcji za pomocą *(Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="c66aa-502">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="c66aa-503">Uruchamianie przykładowej aplikacji, nazwane opcje są zwracane:</span><span class="sxs-lookup"><span data-stu-id="c66aa-503">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="c66aa-504">`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="c66aa-504">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="c66aa-505">`named_options_2`wartości są dostarczane przez:</span><span class="sxs-lookup"><span data-stu-id="c66aa-505">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="c66aa-506">Delegat `named_options_2` w `ConfigureServices` `Option1`dla .</span><span class="sxs-lookup"><span data-stu-id="c66aa-506">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="c66aa-507">Wartość domyślna `Option2` dla `MyOptions` podanych przez klasę.</span><span class="sxs-lookup"><span data-stu-id="c66aa-507">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="c66aa-508">Konfigurowanie wszystkich opcji za pomocą metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="c66aa-508">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="c66aa-509">Skonfiguruj wszystkie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> opcje wystąpień za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="c66aa-509">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="c66aa-510">Poniższy kod `Option1` konfiguruje dla wszystkich wystąpień konfiguracji o wspólnej wartości.</span><span class="sxs-lookup"><span data-stu-id="c66aa-510">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="c66aa-511">Dodaj ręcznie następujący kod `Startup.ConfigureServices` do metody:</span><span class="sxs-lookup"><span data-stu-id="c66aa-511">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="c66aa-512">Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="c66aa-512">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="c66aa-513">Wszystkie opcje są nazwane wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="c66aa-513">All options are named instances.</span></span> <span data-ttu-id="c66aa-514">Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako `Options.DefaultName` kierowanie na `string.Empty`wystąpienie, czyli .</span><span class="sxs-lookup"><span data-stu-id="c66aa-514">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="c66aa-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>również implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="c66aa-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="c66aa-516">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> ma logiki do użycia każdego odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="c66aa-516">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="c66aa-517">Nazwana `null` opcja jest używana do kierowania wszystkich nazwanych wystąpień<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> zamiast <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> określonego nazwanego wystąpienia ( i użyć tej konwencji).</span><span class="sxs-lookup"><span data-stu-id="c66aa-517">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="c66aa-518">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="c66aa-518">OptionsBuilder API</span></span>

<span data-ttu-id="c66aa-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="c66aa-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="c66aa-520">`OptionsBuilder`usprawnia tworzenie nazwanych opcji, ponieważ jest to `AddOptions<TOptions>(string optionsName)` tylko pojedynczy parametr początkowego wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="c66aa-520">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="c66aa-521">Sprawdzanie poprawności `ConfigureOptions` opcji i przeciążenia, które akceptują zależności usług są dostępne tylko za pośrednictwem `OptionsBuilder`programu .</span><span class="sxs-lookup"><span data-stu-id="c66aa-521">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="c66aa-522">Konfigurowanie opcji za pomocą usług DI</span><span class="sxs-lookup"><span data-stu-id="c66aa-522">Use DI services to configure options</span></span>

<span data-ttu-id="c66aa-523">Dostęp do innych usług można uzyskać z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="c66aa-523">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="c66aa-524">Przekaż delegata konfiguracji, aby [skonfigurować](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [opcjach\<TOptions optionsbuilder>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="c66aa-524">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="c66aa-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [Configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) które umożliwiają korzystanie z maksymalnie pięciu usług w celu skonfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="c66aa-526">Utwórz własny typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> który implementuje lub rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="c66aa-526">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="c66aa-527">Zaleca się przekazanie delegata konfiguracji do [configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)ponieważ tworzenie usługi jest bardziej złożone.</span><span class="sxs-lookup"><span data-stu-id="c66aa-527">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="c66aa-528">Tworzenie własnego typu jest równoważne z tym, co robi struktura dla Ciebie podczas korzystania [z funkcji Konfiguruj](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="c66aa-528">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="c66aa-529">Wywołanie [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowy ogólny <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, który ma konstruktora, który akceptuje typy usług ogólnych określonych.</span><span class="sxs-lookup"><span data-stu-id="c66aa-529">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="c66aa-530">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="c66aa-530">Options post-configuration</span></span>

<span data-ttu-id="c66aa-531">Ustaw konfigurację <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>postkonfiguracj za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="c66aa-531">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="c66aa-532">Po zakończeniu konfiguracji <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uruchamia się po zakończeniu całej konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-532">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="c66aa-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępna do konfiguracji po nazwie opcji:</span><span class="sxs-lookup"><span data-stu-id="c66aa-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="c66aa-534">Służy <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> do konfigurowania po skonfigurowaniu wszystkich wystąpień konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="c66aa-534">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="c66aa-535">Uzyskiwanie dostępu do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="c66aa-535">Accessing options during startup</span></span>

<span data-ttu-id="c66aa-536"><xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> może być `Startup.Configure`używany w , `Configure` ponieważ usługi są budowane przed wykonaniem metody.</span><span class="sxs-lookup"><span data-stu-id="c66aa-536"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="c66aa-537">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ani `Startup.ConfigureServices`nie używaj w pliku .</span><span class="sxs-lookup"><span data-stu-id="c66aa-537">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c66aa-538">Niespójne stan opcji może istnieć ze względu na kolejność rejestracji usługi.</span><span class="sxs-lookup"><span data-stu-id="c66aa-538">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c66aa-539">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c66aa-539">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
