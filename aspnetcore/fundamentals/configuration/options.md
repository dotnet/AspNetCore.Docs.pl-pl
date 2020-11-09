---
title: Wzorzec opcji na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać wzorca opcji do reprezentowania grup powiązanych ustawień w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/configuration/options
ms.openlocfilehash: dedc17d7d793a6fd2eac1c8017b704d98a86f1cb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061096"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="dc7a1-103">Wzorzec opcji na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dc7a1-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc7a1-104">[Kirka Larkin](https://twitter.com/serpent5) i [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="dc7a1-105">Wzorzec opcji używa klas, aby zapewnić silnie określony dostęp do grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="dc7a1-106">Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="dc7a1-107">[Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="dc7a1-108">[Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="dc7a1-109">Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="dc7a1-110">Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="dc7a1-111">Ten temat zawiera informacje dotyczące wzorca opcji w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="dc7a1-112">Aby uzyskać informacje na temat używania wzorca opcji w aplikacjach konsolowych, zobacz [Opcje wzorca w programie .NET](/dotnet/core/extensions/options).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="dc7a1-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dc7a1-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="dc7a1-114">Powiąż hierarchiczną konfigurację</span><span class="sxs-lookup"><span data-stu-id="dc7a1-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="dc7a1-115">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-115">Options interfaces</span></span>

<span data-ttu-id="dc7a1-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="dc7a1-117">Czy \* **not** nie_ obsługa: _ odczytywanie danych konfiguracji po rozpoczęciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-117">Does \* **not** _ support: _ Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="dc7a1-118">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="dc7a1-118">Named options</span></span>](#named)
* <span data-ttu-id="dc7a1-119">Jest zarejestrowany jako [pojedynczy](xref:fundamentals/dependency-injection#singleton) i można go wstrzyknąć w dowolnym [okresie istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-119">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="dc7a1-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="dc7a1-121">Jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-121">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="dc7a1-122">Aby uzyskać więcej informacji, zobacz [Użyj IOptionsSnapshot do odczytu zaktualizowanych danych](#ios).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-122">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="dc7a1-123">Jest zarejestrowany jako [zakres](xref:fundamentals/dependency-injection#scoped) i w związku z tym nie można go wstrzyknąć do pojedynczej usługi.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-123">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="dc7a1-124">Obsługuje [nazwane opcje](#named)</span><span class="sxs-lookup"><span data-stu-id="dc7a1-124">Supports [named options](#named)</span></span>

<span data-ttu-id="dc7a1-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="dc7a1-126">Służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-126">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="dc7a1-127">Jest zarejestrowany jako [pojedynczy](xref:fundamentals/dependency-injection#singleton) i można go wstrzyknąć w dowolnym [okresie istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-127">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="dc7a1-128">Uguje</span><span class="sxs-lookup"><span data-stu-id="dc7a1-128">Supports:</span></span>
  * <span data-ttu-id="dc7a1-129">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="dc7a1-129">Change notifications</span></span>
  * [<span data-ttu-id="dc7a1-130">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="dc7a1-130">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="dc7a1-131">Konfiguracja do ponownego ładowania</span><span class="sxs-lookup"><span data-stu-id="dc7a1-131">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="dc7a1-132">Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="dc7a1-132">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="dc7a1-133">Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę opcji po wystąpieniu całej <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-133">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="dc7a1-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> jest odpowiedzialny za tworzenie nowych wystąpień opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="dc7a1-135">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-135">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="dc7a1-136">Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-136">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="dc7a1-137">Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-137">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="dc7a1-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="dc7a1-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-139">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="dc7a1-140">Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-140">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="dc7a1-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-141">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="dc7a1-142">Użyj IOptionsSnapshot do odczytu zaktualizowanych danych</span><span class="sxs-lookup"><span data-stu-id="dc7a1-142">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="dc7a1-143">W przypadku korzystania z <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-143">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="dc7a1-144">Zmiany w konfiguracji są odczytywane po uruchomieniu aplikacji podczas korzystania z dostawców konfiguracji, którzy obsługują odczytywanie zaktualizowanych wartości konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-144">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="dc7a1-145">Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-145">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="dc7a1-146">`IOptionsMonitor` jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-146">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="dc7a1-147">`IOptionsSnapshot` jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie `IOptionsSnapshot<T>` konstruowania obiektu.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-147">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="dc7a1-148">Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-148">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="dc7a1-149">Poniższy kod używa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-149">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="dc7a1-150">Poniższy kod rejestruje wystąpienie konfiguracji, które `MyOptions` wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-150">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="dc7a1-151">W poprzednim kodzie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-151">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="dc7a1-152">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="dc7a1-152">IOptionsMonitor</span></span>

<span data-ttu-id="dc7a1-153">Poniższy kod rejestruje wystąpienie konfiguracji, które `MyOptions` wiąże się z.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-153">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="dc7a1-154">W poniższym przykładzie zastosowano <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-154">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="dc7a1-155">W powyższym kodzie domyślnie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-155">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="dc7a1-156">Obsługa nazwanych opcji przy użyciu IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="dc7a1-156">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="dc7a1-157">Nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-157">Named options:</span></span>

* <span data-ttu-id="dc7a1-158">Są przydatne, gdy wiele sekcji konfiguracji wiąże się z tymi samymi właściwościami.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-158">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="dc7a1-159">Uwzględnia wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-159">Are case sensitive.</span></span>

<span data-ttu-id="dc7a1-160">Weź pod uwagę następujący *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-160">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="dc7a1-161">Zamiast tworzyć dwie klasy do powiązania `TopItem:Month` i `TopItem:Year` , dla każdej sekcji jest używana następująca Klasa:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-161">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="dc7a1-162">Poniższy kod umożliwia skonfigurowanie nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-162">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="dc7a1-163">Poniższy kod wyświetla nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-163">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="dc7a1-164">Wszystkie opcje są nazwanymi wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-164">All options are named instances.</span></span> <span data-ttu-id="dc7a1-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="dc7a1-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="dc7a1-167">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-167">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="dc7a1-168">`null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-168">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="dc7a1-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Użyj tej Konwencji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="dc7a1-170">Interfejs API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="dc7a1-170">OptionsBuilder API</span></span>

<span data-ttu-id="dc7a1-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="dc7a1-172">`OptionsBuilder` usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-172">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="dc7a1-173">Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-173">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="dc7a1-174">`OptionsBuilder` jest używany w sekcji [Walidacja opcji](#val) .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-174">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="dc7a1-175">Korzystanie z usług DI Services w celu konfigurowania opcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-175">Use DI services to configure options</span></span>

<span data-ttu-id="dc7a1-176">Usługi są dostępne z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-176">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="dc7a1-177">Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-177">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="dc7a1-178">`OptionsBuilder<TOptions>` zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , które umożliwiają korzystanie z maksymalnie pięciu usług w celu konfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-178">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="dc7a1-179">Utwórz typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-179">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="dc7a1-180">Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-180">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="dc7a1-181">Tworzenie typu jest równoznaczne z tym, co platforma wykonuje podczas wywoływania [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-181">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="dc7a1-182">Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-182">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="dc7a1-183">Sprawdzanie poprawności opcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-183">Options validation</span></span>

<span data-ttu-id="dc7a1-184">Sprawdzanie poprawności opcji umożliwia zweryfikowanie wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-184">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="dc7a1-185">Weź pod uwagę następujący *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-185">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="dc7a1-186">Następująca Klasa wiąże się z `"MyConfig"` sekcją konfiguracji i stosuje kilka `DataAnnotations` reguł:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-186">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="dc7a1-187">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-187">The following code:</span></span>

* <span data-ttu-id="dc7a1-188">Wywołuje metodę <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> get [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , która wiąże się z `MyConfigOptions` klasą.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="dc7a1-189">Wywołania umożliwiające <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> włączenie walidacji przy użyciu `DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="dc7a1-190">`ValidateDataAnnotations`Metoda rozszerzenia jest zdefiniowana w pakiecie NuGet [Microsoft. Extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-190">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="dc7a1-191">W przypadku aplikacji sieci Web korzystających z `Microsoft.NET.Sdk.Web` zestawu SDK ten pakiet jest przywoływany niejawnie z udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-191">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="dc7a1-192">Poniższy kod wyświetla wartości konfiguracyjne lub błędy walidacji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-192">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="dc7a1-193">Poniższy kod stosuje bardziej złożoną regułę walidacji przy użyciu delegata:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-193">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="dc7a1-194">IValidateOptions na potrzeby złożonej walidacji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-194">IValidateOptions for complex validation</span></span>

<span data-ttu-id="dc7a1-195">Następująca Klasa implementuje <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-195">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="dc7a1-196">`IValidateOptions` umożliwia przeniesienie kodu weryfikacyjnego z `StartUp` i do klasy.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-196">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="dc7a1-197">Przy użyciu powyższego kodu sprawdzanie poprawności jest włączane w programie `Startup.ConfigureServices` przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-197">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="dc7a1-198">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-198">Options post-configuration</span></span>

<span data-ttu-id="dc7a1-199">Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-199">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="dc7a1-200">Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-200">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="dc7a1-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> jest dostępny do po skonfigurowaniu nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="dc7a1-202">Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-202">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="dc7a1-203">Dostęp do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="dc7a1-203">Accessing options during startup</span></span>

<span data-ttu-id="dc7a1-204"><xref:Microsoft.Extensions.Options.IOptions%601> i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-204"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="dc7a1-205">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-205">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc7a1-206">Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-206">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="dc7a1-207">Pakiet NuGet Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="dc7a1-207">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="dc7a1-208">Pakiet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) jest niejawnie przywoływany w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-208">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="dc7a1-209">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-209">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="dc7a1-210">Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-210">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="dc7a1-211">[Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-211">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="dc7a1-212">[Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-212">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="dc7a1-213">Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-213">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="dc7a1-214">Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-214">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="dc7a1-215">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dc7a1-215">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dc7a1-216">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="dc7a1-216">Prerequisites</span></span>

<span data-ttu-id="dc7a1-217">Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-217">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="dc7a1-218">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-218">Options interfaces</span></span>

<span data-ttu-id="dc7a1-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="dc7a1-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> Program obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="dc7a1-221">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="dc7a1-221">Change notifications</span></span>
* [<span data-ttu-id="dc7a1-222">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="dc7a1-222">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="dc7a1-223">Konfiguracja do ponownego ładowania</span><span class="sxs-lookup"><span data-stu-id="dc7a1-223">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="dc7a1-224">Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="dc7a1-224">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="dc7a1-225">Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpieniu całej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-225">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="dc7a1-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> jest odpowiedzialny za tworzenie nowych wystąpień opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="dc7a1-227">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-227">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="dc7a1-228">Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-228">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="dc7a1-229">Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-229">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="dc7a1-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="dc7a1-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-231">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="dc7a1-232">Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-232">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="dc7a1-233"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-233">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="dc7a1-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="dc7a1-235">Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-235">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="dc7a1-236"><xref:Microsoft.Extensions.Options.IOptions%601> może służyć do obsługi opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-236"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="dc7a1-237">Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-237">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="dc7a1-238">Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-238">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="dc7a1-239">Konfiguracja opcji ogólnych</span><span class="sxs-lookup"><span data-stu-id="dc7a1-239">General options configuration</span></span>

<span data-ttu-id="dc7a1-240">Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-240">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="dc7a1-241">Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-241">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="dc7a1-242">Następująca Klasa, `MyOptions` , ma dwie właściwości, `Option1` i `Option2` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-242">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="dc7a1-243">Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-243">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="dc7a1-244">`Option2` ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio ( *modele/opcje. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-244">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="dc7a1-245">`MyOptions`Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-245">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="dc7a1-246">Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z programem <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w celu uzyskania dostępu do ustawień ( *stron/index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-246">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="dc7a1-247">Plik przykładu *appsettings.json* określa wartości dla `option1` i `option2` :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-247">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="dc7a1-248">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-248">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="dc7a1-249">W przypadku używania niestandardowej <xref:System.Configuration.ConfigurationBuilder> konfiguracji opcji do ładowania z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-249">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="dc7a1-250">Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-250">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="dc7a1-251">Skonfiguruj proste opcje z delegatem</span><span class="sxs-lookup"><span data-stu-id="dc7a1-251">Configure simple options with a delegate</span></span>

<span data-ttu-id="dc7a1-252">Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-252">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="dc7a1-253">Użyj delegata, aby ustawić wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-253">Use a delegate to set options values.</span></span> <span data-ttu-id="dc7a1-254">Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy ( *models/MyOptionsWithDelegateConfig. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-254">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="dc7a1-255">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> zostanie dodana druga usługa do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-255">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="dc7a1-256">Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-256">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="dc7a1-257">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-257">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="dc7a1-258">Można dodać wielu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-258">You can add multiple configuration providers.</span></span> <span data-ttu-id="dc7a1-259">Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-259">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="dc7a1-260">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-260">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="dc7a1-261">Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-261">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="dc7a1-262">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w *appsettings.json* , ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-262">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="dc7a1-263">Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-263">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="dc7a1-264">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-264">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="dc7a1-265">Konfiguracja podopcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-265">Suboptions configuration</span></span>

<span data-ttu-id="dc7a1-266">Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-266">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="dc7a1-267">Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-267">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="dc7a1-268">Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-268">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="dc7a1-269">Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-269">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="dc7a1-270">Na przykład `MyOptions.Option1` Właściwość jest powiązana z kluczem `Option1` , który jest odczytywany z `option1` właściwości w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-270">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json* .</span></span>

<span data-ttu-id="dc7a1-271">W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-271">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="dc7a1-272">Wiąże `MySubOptions` się z sekcją `subsection` *appsettings.json* pliku:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-272">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="dc7a1-273">`GetSection`Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-273">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="dc7a1-274">Plik przykładu *appsettings.json* definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-274">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="dc7a1-275">`MySubOptions`Klasa definiuje właściwości `SubOption1` i `SubOption2` , aby przechowywać wartości opcji ( *modele/MySubOptions. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-275">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="dc7a1-276">Metoda modelu strony `OnGet` zwraca ciąg z wartościami opcji ( *Pages/index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-276">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="dc7a1-277">Gdy aplikacja jest uruchomiona, `OnGet` Metoda zwraca ciąg pokazujący wartości klasy podopcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-277">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="dc7a1-278">Iniekcja opcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-278">Options injection</span></span>

<span data-ttu-id="dc7a1-279">Iniekcja opcji jest przedstawiana jako przykład 4 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-279">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="dc7a1-280">Wsuń <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-280">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="dc7a1-281">RazorWidok strony lub MVC z [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-281">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="dc7a1-282">Model strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-282">A page or view model.</span></span>

<span data-ttu-id="dc7a1-283">Poniższy przykład z przykładowej aplikacji wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu strony ( *strony/index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-283">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="dc7a1-284">Przykładowa aplikacja pokazuje, jak wstrzyknąć `IOptionsMonitor<MyOptions>` przy użyciu `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-284">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="dc7a1-285">Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-285">When the app is run, the options values are shown in the rendered page:</span></span>

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="dc7a1-287">Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="dc7a1-287">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="dc7a1-288">Ponowne ładowanie danych konfiguracyjnych przy użyciu programu <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-288">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="dc7a1-289">W przypadku korzystania z <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-289">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="dc7a1-290">Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-290">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="dc7a1-291">`IOptionsMonitor` jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-291">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="dc7a1-292">`IOptionsSnapshot` jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie `IOptionsSnapshot<T>` konstruowania obiektu.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-292">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="dc7a1-293">Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-293">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="dc7a1-294">W poniższym przykładzie pokazano, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> tworzony jest nowy po wprowadzeniu *appsettings.json* zmian ( *Pages/index. cshtml. cs* ).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-294">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="dc7a1-295">Wiele żądań do serwera zwraca wartości stałych dostarczone przez *appsettings.json* plik do momentu zmiany pliku i ponownego załadowania konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-295">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="dc7a1-296">Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z *appsettings.json* pliku:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-296">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="dc7a1-297">Zmień wartości w *appsettings.json* pliku na `value1_from_json UPDATED` i `200` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-297">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="dc7a1-298">Zapisz plik *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-298">Save the *appsettings.json* file.</span></span> <span data-ttu-id="dc7a1-299">Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-299">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="dc7a1-300">Obsługa nazwanych opcji w programie IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="dc7a1-300">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="dc7a1-301">Pomoc techniczna dotycząca opcji nazwanych w programie <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> jest prezentowana jako przykład 6 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-301">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="dc7a1-302">Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-302">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="dc7a1-303">W przykładowej aplikacji, nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions.Configuruj](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która wywołuje [ConfigureNamedOptions \<TOptions> . Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-303">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="dc7a1-304">W nazwanych opcjach jest uwzględniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-304">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="dc7a1-305">Aplikacja Przykładowa uzyskuje dostęp do nazwanych opcji przy użyciu <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *strony/index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-305">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="dc7a1-306">Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-306">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="dc7a1-307">`named_options_1` wartości są dostarczane z konfiguracji, które są ładowane z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-307">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="dc7a1-308">`named_options_2` wartości są podawane przez:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-308">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="dc7a1-309">`named_options_2`Delegat w `ConfigureServices` dla `Option1` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-309">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="dc7a1-310">Wartość domyślna dla `Option2` dostarczonych przez `MyOptions` klasę.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-310">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="dc7a1-311">Skonfiguruj wszystkie opcje przy użyciu metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="dc7a1-311">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="dc7a1-312">Skonfiguruj wszystkie wystąpienia opcji za pomocą <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-312">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="dc7a1-313">Poniższy kod konfiguruje `Option1` wszystkie wystąpienia konfiguracji za pomocą wspólnej wartości.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-313">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="dc7a1-314">Ręcznie Dodaj następujący kod do `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-314">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="dc7a1-315">Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-315">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="dc7a1-316">Wszystkie opcje są nazwanymi wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-316">All options are named instances.</span></span> <span data-ttu-id="dc7a1-317">Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-317">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="dc7a1-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="dc7a1-319">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-319">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="dc7a1-320">`null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-320">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="dc7a1-321">Interfejs API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="dc7a1-321">OptionsBuilder API</span></span>

<span data-ttu-id="dc7a1-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="dc7a1-323">`OptionsBuilder` usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-323">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="dc7a1-324">Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-324">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="dc7a1-325">Korzystanie z usług DI Services w celu konfigurowania opcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-325">Use DI services to configure options</span></span>

<span data-ttu-id="dc7a1-326">Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-326">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="dc7a1-327">Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-327">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="dc7a1-328">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) umożliwiające użycie maksymalnie pięciu usług w celu skonfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="dc7a1-329">Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-329">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="dc7a1-330">Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-330">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="dc7a1-331">Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-331">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="dc7a1-332">Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-332">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="dc7a1-333">Sprawdzanie poprawności opcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-333">Options validation</span></span>

<span data-ttu-id="dc7a1-334">Sprawdzanie poprawności opcji umożliwia sprawdzenie opcji w przypadku skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-334">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="dc7a1-335">Wywołaj `Validate` metodę walidacji zwracającą wartość, `true` Jeśli opcje są prawidłowe i `false` Jeśli są nieprawidłowe:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-335">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="dc7a1-336">Poprzedni przykład ustawia nazwane wystąpienie opcji na `optionalOptionsName` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-336">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="dc7a1-337">Domyślne wystąpienie opcji to `Options.DefaultName` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-337">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="dc7a1-338">Walidacja jest uruchamiana po utworzeniu wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-338">Validation runs when the options instance is created.</span></span> <span data-ttu-id="dc7a1-339">Wystąpienie opcji gwarantuje przekazanie walidacji przy pierwszym dostępie do niego.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-339">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dc7a1-340">Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-340">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="dc7a1-341">Na przykład `IOptionsSnapshot` Opcje są tworzone i sprawdzane jednokrotnie dla każdego żądania po pierwszym uzyskaniu dostępu do tych opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-341">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="dc7a1-342">Te `IOptionsSnapshot` Opcje nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania* .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-342">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request* .</span></span>

<span data-ttu-id="dc7a1-343">`Validate`Metoda akceptuje `Func<TOptions, bool>` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-343">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="dc7a1-344">Aby w pełni dostosować walidację, zaimplementuj `IValidateOptions<TOptions>` , która umożliwia:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-344">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="dc7a1-345">Sprawdzanie poprawności wielu typów opcji: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="dc7a1-345">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="dc7a1-346">Walidacja zależna od innego typu opcji: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="dc7a1-346">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="dc7a1-347">`IValidateOptions` weryfikuje</span><span class="sxs-lookup"><span data-stu-id="dc7a1-347">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="dc7a1-348">Konkretne nazwane wystąpienie opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-348">A specific named options instance.</span></span>
* <span data-ttu-id="dc7a1-349">Wszystkie opcje w `name` przypadku `null` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-349">All options when `name` is `null`.</span></span>

<span data-ttu-id="dc7a1-350">Zwróć `ValidateOptionsResult` z implementacji interfejsu:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-350">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="dc7a1-351">Sprawdzanie poprawności opartej na adnotacji danych jest dostępne w pakiecie [Microsoft. Extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) poprzez wywołanie <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody w `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-351">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="dc7a1-352">`Microsoft.Extensions.Options.DataAnnotations` jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-352">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="dc7a1-353">Eager sprawdzanie poprawności (niepowodzenie szybkie przy uruchamianiu) jest rozważane dla przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-353">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="dc7a1-354">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-354">Options post-configuration</span></span>

<span data-ttu-id="dc7a1-355">Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-355">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="dc7a1-356">Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-356">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="dc7a1-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> jest dostępny do po skonfigurowaniu nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="dc7a1-358">Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-358">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="dc7a1-359">Dostęp do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="dc7a1-359">Accessing options during startup</span></span>

<span data-ttu-id="dc7a1-360"><xref:Microsoft.Extensions.Options.IOptions%601> i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-360"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="dc7a1-361">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-361">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc7a1-362">Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-362">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="dc7a1-363">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-363">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="dc7a1-364">Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-364">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="dc7a1-365">[Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-365">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="dc7a1-366">[Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-366">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="dc7a1-367">Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-367">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="dc7a1-368">Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-368">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="dc7a1-369">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dc7a1-369">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dc7a1-370">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="dc7a1-370">Prerequisites</span></span>

<span data-ttu-id="dc7a1-371">Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-371">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="dc7a1-372">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-372">Options interfaces</span></span>

<span data-ttu-id="dc7a1-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="dc7a1-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> Program obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="dc7a1-375">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="dc7a1-375">Change notifications</span></span>
* [<span data-ttu-id="dc7a1-376">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="dc7a1-376">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="dc7a1-377">Konfiguracja do ponownego ładowania</span><span class="sxs-lookup"><span data-stu-id="dc7a1-377">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="dc7a1-378">Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="dc7a1-378">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="dc7a1-379">Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpieniu całej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-379">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="dc7a1-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> jest odpowiedzialny za tworzenie nowych wystąpień opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="dc7a1-381">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-381">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="dc7a1-382">Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-382">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="dc7a1-383">Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-383">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="dc7a1-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="dc7a1-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-385">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="dc7a1-386">Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-386">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="dc7a1-387"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-387">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="dc7a1-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="dc7a1-389">Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-389">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="dc7a1-390"><xref:Microsoft.Extensions.Options.IOptions%601> może służyć do obsługi opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-390"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="dc7a1-391">Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-391">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="dc7a1-392">Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-392">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="dc7a1-393">Konfiguracja opcji ogólnych</span><span class="sxs-lookup"><span data-stu-id="dc7a1-393">General options configuration</span></span>

<span data-ttu-id="dc7a1-394">Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-394">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="dc7a1-395">Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-395">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="dc7a1-396">Następująca Klasa, `MyOptions` , ma dwie właściwości, `Option1` i `Option2` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-396">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="dc7a1-397">Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-397">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="dc7a1-398">`Option2` ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio ( *modele/opcje. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-398">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="dc7a1-399">`MyOptions`Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-399">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="dc7a1-400">Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z programem <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w celu uzyskania dostępu do ustawień ( *stron/index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-400">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="dc7a1-401">Plik przykładu *appsettings.json* określa wartości dla `option1` i `option2` :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-401">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="dc7a1-402">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-402">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="dc7a1-403">W przypadku używania niestandardowej <xref:System.Configuration.ConfigurationBuilder> konfiguracji opcji do ładowania z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-403">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="dc7a1-404">Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-404">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="dc7a1-405">Skonfiguruj proste opcje z delegatem</span><span class="sxs-lookup"><span data-stu-id="dc7a1-405">Configure simple options with a delegate</span></span>

<span data-ttu-id="dc7a1-406">Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-406">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="dc7a1-407">Użyj delegata, aby ustawić wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-407">Use a delegate to set options values.</span></span> <span data-ttu-id="dc7a1-408">Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy ( *models/MyOptionsWithDelegateConfig. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-408">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="dc7a1-409">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> zostanie dodana druga usługa do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-409">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="dc7a1-410">Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-410">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="dc7a1-411">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-411">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="dc7a1-412">Można dodać wielu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-412">You can add multiple configuration providers.</span></span> <span data-ttu-id="dc7a1-413">Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-413">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="dc7a1-414">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-414">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="dc7a1-415">Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-415">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="dc7a1-416">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w *appsettings.json* , ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-416">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="dc7a1-417">Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-417">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="dc7a1-418">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-418">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="dc7a1-419">Konfiguracja podopcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-419">Suboptions configuration</span></span>

<span data-ttu-id="dc7a1-420">Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-420">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="dc7a1-421">Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-421">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="dc7a1-422">Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-422">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="dc7a1-423">Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-423">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="dc7a1-424">Na przykład `MyOptions.Option1` Właściwość jest powiązana z kluczem `Option1` , który jest odczytywany z `option1` właściwości w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-424">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json* .</span></span>

<span data-ttu-id="dc7a1-425">W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-425">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="dc7a1-426">Wiąże `MySubOptions` się z sekcją `subsection` *appsettings.json* pliku:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-426">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="dc7a1-427">`GetSection`Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-427">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="dc7a1-428">Plik przykładu *appsettings.json* definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="dc7a1-428">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="dc7a1-429">`MySubOptions`Klasa definiuje właściwości `SubOption1` i `SubOption2` , aby przechowywać wartości opcji ( *modele/MySubOptions. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-429">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="dc7a1-430">Metoda modelu strony `OnGet` zwraca ciąg z wartościami opcji ( *Pages/index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-430">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="dc7a1-431">Gdy aplikacja jest uruchomiona, `OnGet` Metoda zwraca ciąg pokazujący wartości klasy podopcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-431">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="dc7a1-432">Opcje udostępniane przez model widoku lub bezpośrednie iniekcja widoku</span><span class="sxs-lookup"><span data-stu-id="dc7a1-432">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="dc7a1-433">Opcje udostępniane przez model widoku lub bezpośrednie wstrzyknięcie widoku są przedstawiane jako przykład 4 w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-433">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="dc7a1-434">Opcje można dostarczyć w modelu widoku lub poprzez wstrzyknięcie <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bezpośrednio do widoku ( *strony/index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-434">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="dc7a1-435">Przykładowa aplikacja pokazuje, jak wstrzyknąć `IOptionsMonitor<MyOptions>` przy użyciu `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-435">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="dc7a1-436">Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-436">When the app is run, the options values are shown in the rendered page:</span></span>

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="dc7a1-438">Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="dc7a1-438">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="dc7a1-439">Ponowne ładowanie danych konfiguracyjnych przy użyciu programu <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-439">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="dc7a1-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> obsługuje opcje ponownego ładowania z minimalnym obciążeniem przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="dc7a1-441">Opcje są obliczane raz dla żądania w przypadku uzyskiwania dostępu do pamięci podręcznej i buforowania jej przez okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-441">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="dc7a1-442">W poniższym przykładzie pokazano, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> tworzony jest nowy po wprowadzeniu *appsettings.json* zmian ( *Pages/index. cshtml. cs* ).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-442">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="dc7a1-443">Wiele żądań do serwera zwraca wartości stałych dostarczone przez *appsettings.json* plik do momentu zmiany pliku i ponownego załadowania konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-443">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="dc7a1-444">Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z *appsettings.json* pliku:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-444">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="dc7a1-445">Zmień wartości w *appsettings.json* pliku na `value1_from_json UPDATED` i `200` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-445">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="dc7a1-446">Zapisz plik *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-446">Save the *appsettings.json* file.</span></span> <span data-ttu-id="dc7a1-447">Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-447">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="dc7a1-448">Obsługa nazwanych opcji w programie IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="dc7a1-448">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="dc7a1-449">Pomoc techniczna dotycząca opcji nazwanych w programie <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> jest prezentowana jako przykład 6 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-449">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="dc7a1-450">Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-450">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="dc7a1-451">W przykładowej aplikacji, nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions.Configuruj](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która wywołuje [ConfigureNamedOptions \<TOptions> . Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-451">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="dc7a1-452">W nazwanych opcjach jest uwzględniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-452">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="dc7a1-453">Aplikacja Przykładowa uzyskuje dostęp do nazwanych opcji przy użyciu <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *strony/index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dc7a1-453">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="dc7a1-454">Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-454">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="dc7a1-455">`named_options_1` wartości są dostarczane z konfiguracji, które są ładowane z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-455">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="dc7a1-456">`named_options_2` wartości są podawane przez:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-456">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="dc7a1-457">`named_options_2`Delegat w `ConfigureServices` dla `Option1` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-457">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="dc7a1-458">Wartość domyślna dla `Option2` dostarczonych przez `MyOptions` klasę.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-458">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="dc7a1-459">Skonfiguruj wszystkie opcje przy użyciu metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="dc7a1-459">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="dc7a1-460">Skonfiguruj wszystkie wystąpienia opcji za pomocą <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-460">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="dc7a1-461">Poniższy kod konfiguruje `Option1` wszystkie wystąpienia konfiguracji za pomocą wspólnej wartości.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-461">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="dc7a1-462">Ręcznie Dodaj następujący kod do `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-462">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="dc7a1-463">Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-463">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="dc7a1-464">Wszystkie opcje są nazwanymi wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-464">All options are named instances.</span></span> <span data-ttu-id="dc7a1-465">Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-465">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="dc7a1-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="dc7a1-467">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-467">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="dc7a1-468">`null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-468">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="dc7a1-469">Interfejs API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="dc7a1-469">OptionsBuilder API</span></span>

<span data-ttu-id="dc7a1-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="dc7a1-471">`OptionsBuilder` usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-471">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="dc7a1-472">Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-472">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="dc7a1-473">Korzystanie z usług DI Services w celu konfigurowania opcji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-473">Use DI services to configure options</span></span>

<span data-ttu-id="dc7a1-474">Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-474">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="dc7a1-475">Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-475">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="dc7a1-476">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) umożliwiające użycie maksymalnie pięciu usług w celu skonfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="dc7a1-477">Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-477">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="dc7a1-478">Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-478">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="dc7a1-479">Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="dc7a1-479">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="dc7a1-480">Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-480">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="dc7a1-481">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dc7a1-481">Options post-configuration</span></span>

<span data-ttu-id="dc7a1-482">Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-482">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="dc7a1-483">Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-483">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="dc7a1-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> jest dostępny do po skonfigurowaniu nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="dc7a1-485">Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dc7a1-485">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="dc7a1-486">Dostęp do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="dc7a1-486">Accessing options during startup</span></span>

<span data-ttu-id="dc7a1-487"><xref:Microsoft.Extensions.Options.IOptions%601> i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-487"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="dc7a1-488">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dc7a1-488">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc7a1-489">Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="dc7a1-489">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="dc7a1-490">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="dc7a1-490">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
