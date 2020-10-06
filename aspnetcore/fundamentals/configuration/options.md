---
title: Wzorzec opcji na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać wzorca opcji do reprezentowania grup powiązanych ustawień w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: 6eafad63bd4c49cee6a85108b37a8b61e1214bce
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762337"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="89f77-103">Wzorzec opcji na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89f77-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89f77-104">[Kirka Larkin](https://twitter.com/serpent5) i [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="89f77-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="89f77-105">Wzorzec opcji używa klas, aby zapewnić silnie określony dostęp do grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="89f77-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="89f77-106">Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="89f77-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="89f77-107">[Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="89f77-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="89f77-108">[Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="89f77-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="89f77-109">Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="89f77-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="89f77-110">Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="89f77-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="89f77-111">Ten temat zawiera informacje dotyczące wzorca opcji w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89f77-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="89f77-112">Aby uzyskać informacje na temat używania wzorca opcji w aplikacjach konsolowych, zobacz [Opcje wzorca w programie .NET](/dotnet/core/extensions/options).</span><span class="sxs-lookup"><span data-stu-id="89f77-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="89f77-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="89f77-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="89f77-114">Powiąż hierarchiczną konfigurację</span><span class="sxs-lookup"><span data-stu-id="89f77-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="89f77-115">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="89f77-115">Options interfaces</span></span>

<span data-ttu-id="89f77-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="89f77-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="89f77-117">Nie ***obsługuje:***</span><span class="sxs-lookup"><span data-stu-id="89f77-117">Does ***not*** support:</span></span>
  * <span data-ttu-id="89f77-118">Odczytywanie danych konfiguracji po rozpoczęciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-118">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="89f77-119">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="89f77-119">Named options</span></span>](#named)
* <span data-ttu-id="89f77-120">Jest zarejestrowany jako [pojedynczy](xref:fundamentals/dependency-injection#singleton) i można go wstrzyknąć w dowolnym [okresie istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="89f77-120">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="89f77-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="89f77-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="89f77-122">Jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="89f77-122">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="89f77-123">Aby uzyskać więcej informacji, zobacz [Użyj IOptionsSnapshot do odczytu zaktualizowanych danych](#ios).</span><span class="sxs-lookup"><span data-stu-id="89f77-123">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="89f77-124">Jest zarejestrowany jako [zakres](xref:fundamentals/dependency-injection#scoped) i w związku z tym nie można go wstrzyknąć do pojedynczej usługi.</span><span class="sxs-lookup"><span data-stu-id="89f77-124">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="89f77-125">Obsługuje [nazwane opcje](#named)</span><span class="sxs-lookup"><span data-stu-id="89f77-125">Supports [named options](#named)</span></span>

<span data-ttu-id="89f77-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="89f77-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="89f77-127">Służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="89f77-127">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="89f77-128">Jest zarejestrowany jako [pojedynczy](xref:fundamentals/dependency-injection#singleton) i można go wstrzyknąć w dowolnym [okresie istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="89f77-128">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="89f77-129">Uguje</span><span class="sxs-lookup"><span data-stu-id="89f77-129">Supports:</span></span>
  * <span data-ttu-id="89f77-130">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="89f77-130">Change notifications</span></span>
  * [<span data-ttu-id="89f77-131">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="89f77-131">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="89f77-132">Konfiguracja do ponownego ładowania</span><span class="sxs-lookup"><span data-stu-id="89f77-132">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="89f77-133">Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="89f77-133">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="89f77-134">Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę opcji po wystąpieniu całej <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-134">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="89f77-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> jest odpowiedzialny za tworzenie nowych wystąpień opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="89f77-136">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="89f77-136">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="89f77-137">Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-137">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="89f77-138">Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="89f77-138">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="89f77-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="89f77-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="89f77-140"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="89f77-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="89f77-141">Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="89f77-141">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="89f77-142"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="89f77-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="89f77-143">Użyj IOptionsSnapshot do odczytu zaktualizowanych danych</span><span class="sxs-lookup"><span data-stu-id="89f77-143">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="89f77-144">W przypadku korzystania z <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="89f77-144">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="89f77-145">Zmiany w konfiguracji są odczytywane po uruchomieniu aplikacji podczas korzystania z dostawców konfiguracji, którzy obsługują odczytywanie zaktualizowanych wartości konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-145">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="89f77-146">Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:</span><span class="sxs-lookup"><span data-stu-id="89f77-146">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="89f77-147">`IOptionsMonitor` jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.</span><span class="sxs-lookup"><span data-stu-id="89f77-147">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="89f77-148">`IOptionsSnapshot` jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie `IOptionsSnapshot<T>` konstruowania obiektu.</span><span class="sxs-lookup"><span data-stu-id="89f77-148">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="89f77-149">Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.</span><span class="sxs-lookup"><span data-stu-id="89f77-149">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="89f77-150">Poniższy kod używa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-150">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="89f77-151">Poniższy kod rejestruje wystąpienie konfiguracji, które `MyOptions` wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="89f77-151">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="89f77-152">W poprzednim kodzie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="89f77-152">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="89f77-153">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="89f77-153">IOptionsMonitor</span></span>

<span data-ttu-id="89f77-154">Poniższy kod rejestruje wystąpienie konfiguracji, które `MyOptions` wiąże się z.</span><span class="sxs-lookup"><span data-stu-id="89f77-154">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="89f77-155">W poniższym przykładzie zastosowano <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="89f77-155">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="89f77-156">W powyższym kodzie domyślnie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="89f77-156">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="89f77-157">Obsługa nazwanych opcji przy użyciu IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="89f77-157">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="89f77-158">Nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="89f77-158">Named options:</span></span>

* <span data-ttu-id="89f77-159">Są przydatne, gdy wiele sekcji konfiguracji wiąże się z tymi samymi właściwościami.</span><span class="sxs-lookup"><span data-stu-id="89f77-159">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="89f77-160">Uwzględnia wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="89f77-160">Are case sensitive.</span></span>

<span data-ttu-id="89f77-161">Rozważmy następujące *appsettings.js* pliku:</span><span class="sxs-lookup"><span data-stu-id="89f77-161">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="89f77-162">Zamiast tworzyć dwie klasy do powiązania `TopItem:Month` i `TopItem:Year` , dla każdej sekcji jest używana następująca Klasa:</span><span class="sxs-lookup"><span data-stu-id="89f77-162">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="89f77-163">Poniższy kod umożliwia skonfigurowanie nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-163">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="89f77-164">Poniższy kod wyświetla nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="89f77-164">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="89f77-165">Wszystkie opcje są nazwanymi wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="89f77-165">All options are named instances.</span></span> <span data-ttu-id="89f77-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="89f77-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="89f77-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="89f77-168">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie.</span><span class="sxs-lookup"><span data-stu-id="89f77-168">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="89f77-169">`null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego.</span><span class="sxs-lookup"><span data-stu-id="89f77-169">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="89f77-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Użyj tej Konwencji.</span><span class="sxs-lookup"><span data-stu-id="89f77-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="89f77-171">Interfejs API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="89f77-171">OptionsBuilder API</span></span>

<span data-ttu-id="89f77-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="89f77-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="89f77-173">`OptionsBuilder` usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="89f77-173">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="89f77-174">Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="89f77-174">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="89f77-175">`OptionsBuilder` jest używany w sekcji [Walidacja opcji](#val) .</span><span class="sxs-lookup"><span data-stu-id="89f77-175">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="89f77-176">Korzystanie z usług DI Services w celu konfigurowania opcji</span><span class="sxs-lookup"><span data-stu-id="89f77-176">Use DI services to configure options</span></span>

<span data-ttu-id="89f77-177">Usługi są dostępne z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="89f77-177">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="89f77-178">Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="89f77-178">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="89f77-179">`OptionsBuilder<TOptions>` zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , które umożliwiają korzystanie z maksymalnie pięciu usług w celu konfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-179">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="89f77-180">Utwórz typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="89f77-180">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="89f77-181">Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane.</span><span class="sxs-lookup"><span data-stu-id="89f77-181">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="89f77-182">Tworzenie typu jest równoznaczne z tym, co platforma wykonuje podczas wywoływania [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="89f77-182">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="89f77-183">Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych.</span><span class="sxs-lookup"><span data-stu-id="89f77-183">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="89f77-184">Sprawdzanie poprawności opcji</span><span class="sxs-lookup"><span data-stu-id="89f77-184">Options validation</span></span>

<span data-ttu-id="89f77-185">Sprawdzanie poprawności opcji umożliwia zweryfikowanie wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-185">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="89f77-186">Rozważmy następujące *appsettings.js* pliku:</span><span class="sxs-lookup"><span data-stu-id="89f77-186">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="89f77-187">Następująca Klasa wiąże się z `"MyConfig"` sekcją konfiguracji i stosuje kilka `DataAnnotations` reguł:</span><span class="sxs-lookup"><span data-stu-id="89f77-187">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="89f77-188">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="89f77-188">The following code:</span></span>

* <span data-ttu-id="89f77-189">Wywołuje metodę <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> get [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , która wiąże się z `MyConfigOptions` klasą.</span><span class="sxs-lookup"><span data-stu-id="89f77-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="89f77-190">Wywołania umożliwiające <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> włączenie walidacji przy użyciu `DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="89f77-190">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="89f77-191">`ValidateDataAnnotations`Metoda rozszerzenia jest zdefiniowana w pakiecie NuGet [Microsoft. Extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="89f77-191">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="89f77-192">W przypadku aplikacji sieci Web korzystających z `Microsoft.NET.Sdk.Web` zestawu SDK ten pakiet jest przywoływany niejawnie z udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="89f77-192">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="89f77-193">Poniższy kod wyświetla wartości konfiguracyjne lub błędy walidacji:</span><span class="sxs-lookup"><span data-stu-id="89f77-193">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="89f77-194">Poniższy kod stosuje bardziej złożoną regułę walidacji przy użyciu delegata:</span><span class="sxs-lookup"><span data-stu-id="89f77-194">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="89f77-195">IValidateOptions na potrzeby złożonej walidacji</span><span class="sxs-lookup"><span data-stu-id="89f77-195">IValidateOptions for complex validation</span></span>

<span data-ttu-id="89f77-196">Następująca Klasa implementuje <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="89f77-196">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="89f77-197">`IValidateOptions` umożliwia przeniesienie kodu weryfikacyjnego z `StartUp` i do klasy.</span><span class="sxs-lookup"><span data-stu-id="89f77-197">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="89f77-198">Przy użyciu powyższego kodu sprawdzanie poprawności jest włączane w programie `Startup.ConfigureServices` przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="89f77-198">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

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

## <a name="options-post-configuration"></a><span data-ttu-id="89f77-199">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="89f77-199">Options post-configuration</span></span>

<span data-ttu-id="89f77-200">Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-200">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="89f77-201">Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="89f77-201">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="89f77-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> jest dostępny do po skonfigurowaniu nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="89f77-203">Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="89f77-203">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="89f77-204">Dostęp do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="89f77-204">Accessing options during startup</span></span>

<span data-ttu-id="89f77-205"><xref:Microsoft.Extensions.Options.IOptions%601> i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="89f77-205"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="89f77-206">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="89f77-206">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="89f77-207">Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="89f77-207">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="89f77-208">Pakiet NuGet Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="89f77-208">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="89f77-209">Pakiet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) jest niejawnie przywoływany w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89f77-209">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="89f77-210">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="89f77-210">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="89f77-211">Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="89f77-211">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="89f77-212">[Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="89f77-212">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="89f77-213">[Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="89f77-213">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="89f77-214">Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="89f77-214">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="89f77-215">Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="89f77-215">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="89f77-216">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="89f77-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="89f77-217">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="89f77-217">Prerequisites</span></span>

<span data-ttu-id="89f77-218">Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="89f77-218">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="89f77-219">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="89f77-219">Options interfaces</span></span>

<span data-ttu-id="89f77-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="89f77-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="89f77-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> Program obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="89f77-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="89f77-222">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="89f77-222">Change notifications</span></span>
* [<span data-ttu-id="89f77-223">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="89f77-223">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="89f77-224">Konfiguracja do ponownego ładowania</span><span class="sxs-lookup"><span data-stu-id="89f77-224">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="89f77-225">Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="89f77-225">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="89f77-226">Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpieniu całej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-226">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="89f77-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> jest odpowiedzialny za tworzenie nowych wystąpień opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="89f77-228">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="89f77-228">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="89f77-229">Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-229">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="89f77-230">Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="89f77-230">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="89f77-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="89f77-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="89f77-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="89f77-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="89f77-233">Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="89f77-233">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="89f77-234"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="89f77-234">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="89f77-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="89f77-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="89f77-236">Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="89f77-236">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="89f77-237"><xref:Microsoft.Extensions.Options.IOptions%601> może służyć do obsługi opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-237"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="89f77-238">Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-238">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="89f77-239">Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-239">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="89f77-240">Konfiguracja opcji ogólnych</span><span class="sxs-lookup"><span data-stu-id="89f77-240">General options configuration</span></span>

<span data-ttu-id="89f77-241">Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="89f77-241">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="89f77-242">Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="89f77-242">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="89f77-243">Następująca Klasa, `MyOptions` , ma dwie właściwości, `Option1` i `Option2` .</span><span class="sxs-lookup"><span data-stu-id="89f77-243">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="89f77-244">Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1` .</span><span class="sxs-lookup"><span data-stu-id="89f77-244">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="89f77-245">`Option2` ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio (*modele/opcje. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-245">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="89f77-246">`MyOptions`Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="89f77-246">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="89f77-247">Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z programem <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w celu uzyskania dostępu do ustawień (*stron/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-247">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="89f77-248">*appsettings.jspróbki na* pliku określa wartości dla `option1` i `option2` :</span><span class="sxs-lookup"><span data-stu-id="89f77-248">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="89f77-249">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-249">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="89f77-250">W przypadku używania niestandardowej <xref:System.Configuration.ConfigurationBuilder> konfiguracji opcji do ładowania z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:</span><span class="sxs-lookup"><span data-stu-id="89f77-250">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="89f77-251">Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="89f77-251">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="89f77-252">Skonfiguruj proste opcje z delegatem</span><span class="sxs-lookup"><span data-stu-id="89f77-252">Configure simple options with a delegate</span></span>

<span data-ttu-id="89f77-253">Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-253">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="89f77-254">Użyj delegata, aby ustawić wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-254">Use a delegate to set options values.</span></span> <span data-ttu-id="89f77-255">Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy (*models/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-255">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="89f77-256">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> zostanie dodana druga usługa do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="89f77-256">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="89f77-257">Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="89f77-257">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="89f77-258">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="89f77-258">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="89f77-259">Można dodać wielu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-259">You can add multiple configuration providers.</span></span> <span data-ttu-id="89f77-260">Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="89f77-260">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="89f77-261">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="89f77-261">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="89f77-262">Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="89f77-262">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="89f77-263">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w *appsettings.json*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="89f77-263">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="89f77-264">Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-264">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="89f77-265">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-265">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="89f77-266">Konfiguracja podopcji</span><span class="sxs-lookup"><span data-stu-id="89f77-266">Suboptions configuration</span></span>

<span data-ttu-id="89f77-267">Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-267">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="89f77-268">Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-268">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="89f77-269">Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.</span><span class="sxs-lookup"><span data-stu-id="89f77-269">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="89f77-270">Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="89f77-270">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="89f77-271">Na przykład `MyOptions.Option1` Właściwość jest powiązana z kluczem `Option1` , który jest odczytywany z `option1` właściwości w *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="89f77-271">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="89f77-272">W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="89f77-272">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="89f77-273">Wiąże `MySubOptions` się z sekcją `subsection` *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="89f77-273">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="89f77-274">`GetSection`Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="89f77-274">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="89f77-275">*appsettings.jspróbki w* pliku definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="89f77-275">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="89f77-276">`MySubOptions`Klasa definiuje właściwości `SubOption1` i `SubOption2` , aby przechowywać wartości opcji (*modele/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-276">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="89f77-277">Metoda modelu strony `OnGet` zwraca ciąg z wartościami opcji (*Pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-277">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="89f77-278">Gdy aplikacja jest uruchomiona, `OnGet` Metoda zwraca ciąg pokazujący wartości klasy podopcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-278">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="89f77-279">Iniekcja opcji</span><span class="sxs-lookup"><span data-stu-id="89f77-279">Options injection</span></span>

<span data-ttu-id="89f77-280">Iniekcja opcji jest przedstawiana jako przykład 4 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-280">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="89f77-281">Wsuń <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="89f77-281">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="89f77-282">RazorWidok strony lub MVC z [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="89f77-282">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="89f77-283">Model strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="89f77-283">A page or view model.</span></span>

<span data-ttu-id="89f77-284">Poniższy przykład z przykładowej aplikacji wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu strony (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-284">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="89f77-285">Przykładowa aplikacja pokazuje, jak wstrzyknąć `IOptionsMonitor<MyOptions>` przy użyciu `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="89f77-285">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="89f77-286">Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-286">When the app is run, the options values are shown in the rendered page:</span></span>

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="89f77-288">Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="89f77-288">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="89f77-289">Ponowne ładowanie danych konfiguracyjnych przy użyciu programu <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-289">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="89f77-290">W przypadku korzystania z <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="89f77-290">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="89f77-291">Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:</span><span class="sxs-lookup"><span data-stu-id="89f77-291">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="89f77-292">`IOptionsMonitor` jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.</span><span class="sxs-lookup"><span data-stu-id="89f77-292">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="89f77-293">`IOptionsSnapshot` jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie `IOptionsSnapshot<T>` konstruowania obiektu.</span><span class="sxs-lookup"><span data-stu-id="89f77-293">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="89f77-294">Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.</span><span class="sxs-lookup"><span data-stu-id="89f77-294">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="89f77-295">W poniższym przykładzie pokazano, jak nowa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest tworzona po *appsettings.js* zmian (*Pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="89f77-295">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="89f77-296">Wiele żądań do serwera zwraca wartości stałych dostarczone przez *appsettings.jsw* pliku do momentu zmiany pliku i ponownego załadowania konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-296">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="89f77-297">Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="89f77-297">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="89f77-298">Zmień wartości w *appsettings.js* w pliku na `value1_from_json UPDATED` i `200` .</span><span class="sxs-lookup"><span data-stu-id="89f77-298">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="89f77-299">Zapisz *appsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="89f77-299">Save the *appsettings.json* file.</span></span> <span data-ttu-id="89f77-300">Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="89f77-300">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="89f77-301">Obsługa nazwanych opcji w programie IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="89f77-301">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="89f77-302">Pomoc techniczna dotycząca opcji nazwanych w programie <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> jest prezentowana jako przykład 6 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-302">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="89f77-303">Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-303">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="89f77-304">W przykładowej aplikacji, nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions.Configuruj](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która wywołuje [ConfigureNamedOptions \<TOptions> . Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="89f77-304">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="89f77-305">W nazwanych opcjach jest uwzględniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="89f77-305">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="89f77-306">Aplikacja Przykładowa uzyskuje dostęp do nazwanych opcji przy użyciu <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-306">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="89f77-307">Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="89f77-307">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="89f77-308">`named_options_1` wartości są dostarczane z konfiguracji, które są ładowane z *appsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="89f77-308">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="89f77-309">`named_options_2` wartości są podawane przez:</span><span class="sxs-lookup"><span data-stu-id="89f77-309">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="89f77-310">`named_options_2`Delegat w `ConfigureServices` dla `Option1` .</span><span class="sxs-lookup"><span data-stu-id="89f77-310">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="89f77-311">Wartość domyślna dla `Option2` dostarczonych przez `MyOptions` klasę.</span><span class="sxs-lookup"><span data-stu-id="89f77-311">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="89f77-312">Skonfiguruj wszystkie opcje przy użyciu metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="89f77-312">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="89f77-313">Skonfiguruj wszystkie wystąpienia opcji za pomocą <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="89f77-313">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="89f77-314">Poniższy kod konfiguruje `Option1` wszystkie wystąpienia konfiguracji za pomocą wspólnej wartości.</span><span class="sxs-lookup"><span data-stu-id="89f77-314">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="89f77-315">Ręcznie Dodaj następujący kod do `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="89f77-315">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="89f77-316">Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="89f77-316">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="89f77-317">Wszystkie opcje są nazwanymi wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="89f77-317">All options are named instances.</span></span> <span data-ttu-id="89f77-318">Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="89f77-318">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="89f77-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="89f77-320">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie.</span><span class="sxs-lookup"><span data-stu-id="89f77-320">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="89f77-321">`null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).</span><span class="sxs-lookup"><span data-stu-id="89f77-321">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="89f77-322">Interfejs API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="89f77-322">OptionsBuilder API</span></span>

<span data-ttu-id="89f77-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="89f77-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="89f77-324">`OptionsBuilder` usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="89f77-324">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="89f77-325">Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="89f77-325">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="89f77-326">Korzystanie z usług DI Services w celu konfigurowania opcji</span><span class="sxs-lookup"><span data-stu-id="89f77-326">Use DI services to configure options</span></span>

<span data-ttu-id="89f77-327">Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="89f77-327">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="89f77-328">Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="89f77-328">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="89f77-329">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) umożliwiające użycie maksymalnie pięciu usług w celu skonfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-329">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="89f77-330">Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="89f77-330">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="89f77-331">Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane.</span><span class="sxs-lookup"><span data-stu-id="89f77-331">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="89f77-332">Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="89f77-332">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="89f77-333">Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych.</span><span class="sxs-lookup"><span data-stu-id="89f77-333">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="89f77-334">Sprawdzanie poprawności opcji</span><span class="sxs-lookup"><span data-stu-id="89f77-334">Options validation</span></span>

<span data-ttu-id="89f77-335">Sprawdzanie poprawności opcji umożliwia sprawdzenie opcji w przypadku skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-335">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="89f77-336">Wywołaj `Validate` metodę walidacji zwracającą wartość, `true` Jeśli opcje są prawidłowe i `false` Jeśli są nieprawidłowe:</span><span class="sxs-lookup"><span data-stu-id="89f77-336">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="89f77-337">Poprzedni przykład ustawia nazwane wystąpienie opcji na `optionalOptionsName` .</span><span class="sxs-lookup"><span data-stu-id="89f77-337">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="89f77-338">Domyślne wystąpienie opcji to `Options.DefaultName` .</span><span class="sxs-lookup"><span data-stu-id="89f77-338">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="89f77-339">Walidacja jest uruchamiana po utworzeniu wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-339">Validation runs when the options instance is created.</span></span> <span data-ttu-id="89f77-340">Wystąpienie opcji gwarantuje przekazanie walidacji przy pierwszym dostępie do niego.</span><span class="sxs-lookup"><span data-stu-id="89f77-340">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="89f77-341">Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-341">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="89f77-342">Na przykład `IOptionsSnapshot` Opcje są tworzone i sprawdzane jednokrotnie dla każdego żądania po pierwszym uzyskaniu dostępu do tych opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-342">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="89f77-343">Te `IOptionsSnapshot` Opcje nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania*.</span><span class="sxs-lookup"><span data-stu-id="89f77-343">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="89f77-344">`Validate`Metoda akceptuje `Func<TOptions, bool>` .</span><span class="sxs-lookup"><span data-stu-id="89f77-344">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="89f77-345">Aby w pełni dostosować walidację, zaimplementuj `IValidateOptions<TOptions>` , która umożliwia:</span><span class="sxs-lookup"><span data-stu-id="89f77-345">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="89f77-346">Sprawdzanie poprawności wielu typów opcji: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="89f77-346">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="89f77-347">Walidacja zależna od innego typu opcji: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="89f77-347">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="89f77-348">`IValidateOptions` weryfikuje</span><span class="sxs-lookup"><span data-stu-id="89f77-348">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="89f77-349">Konkretne nazwane wystąpienie opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-349">A specific named options instance.</span></span>
* <span data-ttu-id="89f77-350">Wszystkie opcje w `name` przypadku `null` .</span><span class="sxs-lookup"><span data-stu-id="89f77-350">All options when `name` is `null`.</span></span>

<span data-ttu-id="89f77-351">Zwróć `ValidateOptionsResult` z implementacji interfejsu:</span><span class="sxs-lookup"><span data-stu-id="89f77-351">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="89f77-352">Sprawdzanie poprawności opartej na adnotacji danych jest dostępne w pakiecie [Microsoft. Extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) poprzez wywołanie <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody w `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="89f77-352">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="89f77-353">`Microsoft.Extensions.Options.DataAnnotations` jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="89f77-353">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="89f77-354">Eager sprawdzanie poprawności (niepowodzenie szybkie przy uruchamianiu) jest rozważane dla przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="89f77-354">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="89f77-355">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="89f77-355">Options post-configuration</span></span>

<span data-ttu-id="89f77-356">Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-356">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="89f77-357">Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="89f77-357">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="89f77-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> jest dostępny do po skonfigurowaniu nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="89f77-359">Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="89f77-359">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="89f77-360">Dostęp do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="89f77-360">Accessing options during startup</span></span>

<span data-ttu-id="89f77-361"><xref:Microsoft.Extensions.Options.IOptions%601> i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="89f77-361"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="89f77-362">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="89f77-362">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="89f77-363">Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="89f77-363">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="89f77-364">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="89f77-364">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="89f77-365">Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="89f77-365">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="89f77-366">[Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="89f77-366">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="89f77-367">[Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="89f77-367">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="89f77-368">Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="89f77-368">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="89f77-369">Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="89f77-369">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="89f77-370">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="89f77-370">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="89f77-371">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="89f77-371">Prerequisites</span></span>

<span data-ttu-id="89f77-372">Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="89f77-372">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="89f77-373">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="89f77-373">Options interfaces</span></span>

<span data-ttu-id="89f77-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="89f77-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="89f77-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> Program obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="89f77-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="89f77-376">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="89f77-376">Change notifications</span></span>
* [<span data-ttu-id="89f77-377">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="89f77-377">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="89f77-378">Konfiguracja do ponownego ładowania</span><span class="sxs-lookup"><span data-stu-id="89f77-378">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="89f77-379">Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="89f77-379">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="89f77-380">Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpieniu całej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-380">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="89f77-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> jest odpowiedzialny za tworzenie nowych wystąpień opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="89f77-382">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="89f77-382">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="89f77-383">Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-383">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="89f77-384">Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="89f77-384">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="89f77-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="89f77-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="89f77-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="89f77-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="89f77-387">Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="89f77-387">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="89f77-388"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="89f77-388">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="89f77-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="89f77-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="89f77-390">Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="89f77-390">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="89f77-391"><xref:Microsoft.Extensions.Options.IOptions%601> może służyć do obsługi opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-391"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="89f77-392">Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-392">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="89f77-393">Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-393">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="89f77-394">Konfiguracja opcji ogólnych</span><span class="sxs-lookup"><span data-stu-id="89f77-394">General options configuration</span></span>

<span data-ttu-id="89f77-395">Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="89f77-395">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="89f77-396">Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="89f77-396">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="89f77-397">Następująca Klasa, `MyOptions` , ma dwie właściwości, `Option1` i `Option2` .</span><span class="sxs-lookup"><span data-stu-id="89f77-397">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="89f77-398">Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1` .</span><span class="sxs-lookup"><span data-stu-id="89f77-398">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="89f77-399">`Option2` ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio (*modele/opcje. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-399">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="89f77-400">`MyOptions`Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="89f77-400">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="89f77-401">Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z programem <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w celu uzyskania dostępu do ustawień (*stron/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-401">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="89f77-402">*appsettings.jspróbki na* pliku określa wartości dla `option1` i `option2` :</span><span class="sxs-lookup"><span data-stu-id="89f77-402">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="89f77-403">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-403">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="89f77-404">W przypadku używania niestandardowej <xref:System.Configuration.ConfigurationBuilder> konfiguracji opcji do ładowania z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:</span><span class="sxs-lookup"><span data-stu-id="89f77-404">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="89f77-405">Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="89f77-405">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="89f77-406">Skonfiguruj proste opcje z delegatem</span><span class="sxs-lookup"><span data-stu-id="89f77-406">Configure simple options with a delegate</span></span>

<span data-ttu-id="89f77-407">Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-407">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="89f77-408">Użyj delegata, aby ustawić wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-408">Use a delegate to set options values.</span></span> <span data-ttu-id="89f77-409">Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy (*models/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-409">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="89f77-410">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> zostanie dodana druga usługa do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="89f77-410">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="89f77-411">Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="89f77-411">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="89f77-412">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="89f77-412">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="89f77-413">Można dodać wielu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-413">You can add multiple configuration providers.</span></span> <span data-ttu-id="89f77-414">Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="89f77-414">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="89f77-415">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="89f77-415">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="89f77-416">Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="89f77-416">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="89f77-417">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w *appsettings.json*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="89f77-417">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="89f77-418">Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-418">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="89f77-419">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-419">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="89f77-420">Konfiguracja podopcji</span><span class="sxs-lookup"><span data-stu-id="89f77-420">Suboptions configuration</span></span>

<span data-ttu-id="89f77-421">Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-421">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="89f77-422">Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-422">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="89f77-423">Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.</span><span class="sxs-lookup"><span data-stu-id="89f77-423">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="89f77-424">Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="89f77-424">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="89f77-425">Na przykład `MyOptions.Option1` Właściwość jest powiązana z kluczem `Option1` , który jest odczytywany z `option1` właściwości w *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="89f77-425">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="89f77-426">W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="89f77-426">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="89f77-427">Wiąże `MySubOptions` się z sekcją `subsection` *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="89f77-427">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="89f77-428">`GetSection`Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="89f77-428">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="89f77-429">*appsettings.jspróbki w* pliku definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="89f77-429">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="89f77-430">`MySubOptions`Klasa definiuje właściwości `SubOption1` i `SubOption2` , aby przechowywać wartości opcji (*modele/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-430">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="89f77-431">Metoda modelu strony `OnGet` zwraca ciąg z wartościami opcji (*Pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-431">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="89f77-432">Gdy aplikacja jest uruchomiona, `OnGet` Metoda zwraca ciąg pokazujący wartości klasy podopcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-432">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="89f77-433">Opcje udostępniane przez model widoku lub bezpośrednie iniekcja widoku</span><span class="sxs-lookup"><span data-stu-id="89f77-433">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="89f77-434">Opcje udostępniane przez model widoku lub bezpośrednie wstrzyknięcie widoku są przedstawiane jako przykład 4 w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="89f77-434">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="89f77-435">Opcje można dostarczyć w modelu widoku lub poprzez wstrzyknięcie <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bezpośrednio do widoku (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-435">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="89f77-436">Przykładowa aplikacja pokazuje, jak wstrzyknąć `IOptionsMonitor<MyOptions>` przy użyciu `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="89f77-436">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="89f77-437">Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-437">When the app is run, the options values are shown in the rendered page:</span></span>

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="89f77-439">Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="89f77-439">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="89f77-440">Ponowne ładowanie danych konfiguracyjnych przy użyciu programu <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-440">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="89f77-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> obsługuje opcje ponownego ładowania z minimalnym obciążeniem przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="89f77-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="89f77-442">Opcje są obliczane raz dla żądania w przypadku uzyskiwania dostępu do pamięci podręcznej i buforowania jej przez okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="89f77-442">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="89f77-443">W poniższym przykładzie pokazano, jak nowa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest tworzona po *appsettings.js* zmian (*Pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="89f77-443">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="89f77-444">Wiele żądań do serwera zwraca wartości stałych dostarczone przez *appsettings.jsw* pliku do momentu zmiany pliku i ponownego załadowania konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89f77-444">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="89f77-445">Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="89f77-445">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="89f77-446">Zmień wartości w *appsettings.js* w pliku na `value1_from_json UPDATED` i `200` .</span><span class="sxs-lookup"><span data-stu-id="89f77-446">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="89f77-447">Zapisz *appsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="89f77-447">Save the *appsettings.json* file.</span></span> <span data-ttu-id="89f77-448">Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="89f77-448">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="89f77-449">Obsługa nazwanych opcji w programie IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="89f77-449">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="89f77-450">Pomoc techniczna dotycząca opcji nazwanych w programie <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> jest prezentowana jako przykład 6 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89f77-450">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="89f77-451">Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji.</span><span class="sxs-lookup"><span data-stu-id="89f77-451">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="89f77-452">W przykładowej aplikacji, nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions.Configuruj](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która wywołuje [ConfigureNamedOptions \<TOptions> . Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="89f77-452">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="89f77-453">W nazwanych opcjach jest uwzględniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="89f77-453">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="89f77-454">Aplikacja Przykładowa uzyskuje dostęp do nazwanych opcji przy użyciu <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="89f77-454">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="89f77-455">Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="89f77-455">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="89f77-456">`named_options_1` wartości są dostarczane z konfiguracji, które są ładowane z *appsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="89f77-456">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="89f77-457">`named_options_2` wartości są podawane przez:</span><span class="sxs-lookup"><span data-stu-id="89f77-457">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="89f77-458">`named_options_2`Delegat w `ConfigureServices` dla `Option1` .</span><span class="sxs-lookup"><span data-stu-id="89f77-458">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="89f77-459">Wartość domyślna dla `Option2` dostarczonych przez `MyOptions` klasę.</span><span class="sxs-lookup"><span data-stu-id="89f77-459">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="89f77-460">Skonfiguruj wszystkie opcje przy użyciu metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="89f77-460">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="89f77-461">Skonfiguruj wszystkie wystąpienia opcji za pomocą <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="89f77-461">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="89f77-462">Poniższy kod konfiguruje `Option1` wszystkie wystąpienia konfiguracji za pomocą wspólnej wartości.</span><span class="sxs-lookup"><span data-stu-id="89f77-462">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="89f77-463">Ręcznie Dodaj następujący kod do `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="89f77-463">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="89f77-464">Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="89f77-464">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="89f77-465">Wszystkie opcje są nazwanymi wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="89f77-465">All options are named instances.</span></span> <span data-ttu-id="89f77-466">Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="89f77-466">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="89f77-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="89f77-468">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie.</span><span class="sxs-lookup"><span data-stu-id="89f77-468">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="89f77-469">`null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).</span><span class="sxs-lookup"><span data-stu-id="89f77-469">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="89f77-470">Interfejs API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="89f77-470">OptionsBuilder API</span></span>

<span data-ttu-id="89f77-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="89f77-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="89f77-472">`OptionsBuilder` usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="89f77-472">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="89f77-473">Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="89f77-473">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="89f77-474">Korzystanie z usług DI Services w celu konfigurowania opcji</span><span class="sxs-lookup"><span data-stu-id="89f77-474">Use DI services to configure options</span></span>

<span data-ttu-id="89f77-475">Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="89f77-475">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="89f77-476">Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="89f77-476">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="89f77-477">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) umożliwiające użycie maksymalnie pięciu usług w celu skonfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-477">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="89f77-478">Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="89f77-478">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="89f77-479">Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane.</span><span class="sxs-lookup"><span data-stu-id="89f77-479">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="89f77-480">Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="89f77-480">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="89f77-481">Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych.</span><span class="sxs-lookup"><span data-stu-id="89f77-481">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="89f77-482">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="89f77-482">Options post-configuration</span></span>

<span data-ttu-id="89f77-483">Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="89f77-483">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="89f77-484">Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="89f77-484">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="89f77-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> jest dostępny do po skonfigurowaniu nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="89f77-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="89f77-486">Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="89f77-486">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="89f77-487">Dostęp do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="89f77-487">Accessing options during startup</span></span>

<span data-ttu-id="89f77-488"><xref:Microsoft.Extensions.Options.IOptions%601> i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="89f77-488"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="89f77-489">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="89f77-489">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="89f77-490">Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="89f77-490">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="89f77-491">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="89f77-491">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
