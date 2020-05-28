---
<span data-ttu-id="b6d5e-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6d5e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6d5e-102">'Blazor'</span></span>
- <span data-ttu-id="b6d5e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6d5e-103">'Identity'</span></span>
- <span data-ttu-id="b6d5e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6d5e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6d5e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6d5e-105">'Razor'</span></span>
- <span data-ttu-id="b6d5e-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-106">'SignalR' uid:</span></span> 

--- 
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="b6d5e-107">Wzorzec opcji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6d5e-107">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b6d5e-108">[Kirka Larkin](https://twitter.com/serpent5) i [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-108">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="b6d5e-109">Wzorzec opcji używa klas, aby zapewnić silnie określony dostęp do grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-109">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="b6d5e-110">Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-110">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="b6d5e-111">[Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-111">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="b6d5e-112">[Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-112">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="b6d5e-113">Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-113">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="b6d5e-114">Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-114">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="b6d5e-115">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b6d5e-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="b6d5e-116">Powiąż hierarchiczną konfigurację</span><span class="sxs-lookup"><span data-stu-id="b6d5e-116">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="b6d5e-117">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-117">Options interfaces</span></span>

<span data-ttu-id="b6d5e-118"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-118"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="b6d5e-119">Nie ***obsługuje:***</span><span class="sxs-lookup"><span data-stu-id="b6d5e-119">Does ***not*** support:</span></span>
  * <span data-ttu-id="b6d5e-120">Odczytywanie danych konfiguracji po rozpoczęciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-120">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="b6d5e-121">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="b6d5e-121">Named options</span></span>](#named)
* <span data-ttu-id="b6d5e-122">Jest zarejestrowany jako [pojedynczy](xref:fundamentals/dependency-injection#singleton) i można go wstrzyknąć w dowolnym [okresie istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-122">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="b6d5e-123"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-123"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="b6d5e-124">Jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-124">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="b6d5e-125">Aby uzyskać więcej informacji, zobacz [Użyj IOptionsSnapshot do odczytu zaktualizowanych danych](#ios).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-125">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="b6d5e-126">Jest zarejestrowany jako [zakres](xref:fundamentals/dependency-injection#scoped) i w związku z tym nie można go wstrzyknąć do pojedynczej usługi.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-126">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="b6d5e-127">Obsługuje [nazwane opcje](#named)</span><span class="sxs-lookup"><span data-stu-id="b6d5e-127">Supports [named options](#named)</span></span>

<span data-ttu-id="b6d5e-128"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-128"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="b6d5e-129">Służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-129">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="b6d5e-130">Jest zarejestrowany jako [pojedynczy](xref:fundamentals/dependency-injection#singleton) i można go wstrzyknąć w dowolnym [okresie istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-130">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="b6d5e-131">Uguje</span><span class="sxs-lookup"><span data-stu-id="b6d5e-131">Supports:</span></span>
  * <span data-ttu-id="b6d5e-132">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="b6d5e-132">Change notifications</span></span>
  * [<span data-ttu-id="b6d5e-133">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="b6d5e-133">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="b6d5e-134">Konfiguracja do ponownego ładowania</span><span class="sxs-lookup"><span data-stu-id="b6d5e-134">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="b6d5e-135">Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="b6d5e-135">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="b6d5e-136">Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę opcji po wystąpieniu całej <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-136">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="b6d5e-137"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych wystąpień opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-137"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="b6d5e-138">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-138">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="b6d5e-139">Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-139">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="b6d5e-140">Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-140">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="b6d5e-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="b6d5e-142"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="b6d5e-143">Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-143">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="b6d5e-144"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-144">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="b6d5e-145">Użyj IOptionsSnapshot do odczytu zaktualizowanych danych</span><span class="sxs-lookup"><span data-stu-id="b6d5e-145">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="b6d5e-146">W przypadku korzystania z <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-146">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="b6d5e-147">Zmiany w konfiguracji są odczytywane po uruchomieniu aplikacji podczas korzystania z dostawców konfiguracji, którzy obsługują odczytywanie zaktualizowanych wartości konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-147">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="b6d5e-148">Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-148">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="b6d5e-149">`IOptionsMonitor`jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-149">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="b6d5e-150">`IOptionsSnapshot`jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie `IOptionsSnapshot<T>` konstruowania obiektu.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-150">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="b6d5e-151">Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-151">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="b6d5e-152">Poniższy kod używa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-152">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="b6d5e-153">Poniższy kod rejestruje wystąpienie konfiguracji, które `MyOptions` wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-153">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="b6d5e-154">W poprzednim kodzie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-154">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="b6d5e-155">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="b6d5e-155">IOptionsMonitor</span></span>

<span data-ttu-id="b6d5e-156">Poniższy kod rejestruje wystąpienie konfiguracji, które `MyOptions` wiąże się z.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-156">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="b6d5e-157">W poniższym przykładzie zastosowano <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-157">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="b6d5e-158">W powyższym kodzie domyślnie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-158">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="b6d5e-159">Obsługa nazwanych opcji przy użyciu IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="b6d5e-159">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="b6d5e-160">Nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-160">Named options:</span></span>

* <span data-ttu-id="b6d5e-161">Są przydatne, gdy wiele sekcji konfiguracji wiąże się z tymi samymi właściwościami.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-161">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="b6d5e-162">Uwzględnia wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-162">Are case sensitive.</span></span>

<span data-ttu-id="b6d5e-163">Rozważmy następujący plik *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-163">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="b6d5e-164">Zamiast tworzyć dwie klasy do powiązania `TopItem:Month` i `TopItem:Year` , dla każdej sekcji jest używana następująca Klasa:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-164">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="b6d5e-165">Poniższy kod umożliwia skonfigurowanie nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-165">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="b6d5e-166">Poniższy kod wyświetla nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-166">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="b6d5e-167">Wszystkie opcje są nazwanymi wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-167">All options are named instances.</span></span> <span data-ttu-id="b6d5e-168"><xref:Microsoft.Extensions.Options.IConfigureOptions%601>wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-168"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="b6d5e-169"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-169"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="b6d5e-170">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-170">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="b6d5e-171">`null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-171">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="b6d5e-172"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Użyj tej Konwencji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-172"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="b6d5e-173">Interfejs API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="b6d5e-173">OptionsBuilder API</span></span>

<span data-ttu-id="b6d5e-174"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-174"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="b6d5e-175">`OptionsBuilder`usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-175">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="b6d5e-176">Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-176">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="b6d5e-177">`OptionsBuilder`jest używany w sekcji [Walidacja opcji](#val) .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-177">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="b6d5e-178">Korzystanie z usług DI Services w celu konfigurowania opcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-178">Use DI services to configure options</span></span>

<span data-ttu-id="b6d5e-179">Usługi są dostępne z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-179">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="b6d5e-180">Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-180">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="b6d5e-181">`OptionsBuilder<TOptions>`zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , które umożliwiają korzystanie z maksymalnie pięciu usług w celu konfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-181">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="b6d5e-182">Utwórz typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-182">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="b6d5e-183">Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-183">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="b6d5e-184">Tworzenie typu jest równoznaczne z tym, co platforma wykonuje podczas wywoływania [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-184">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="b6d5e-185">Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-185">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="b6d5e-186">Sprawdzanie poprawności opcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-186">Options validation</span></span>

<span data-ttu-id="b6d5e-187">Sprawdzanie poprawności opcji umożliwia zweryfikowanie wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-187">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="b6d5e-188">Rozważmy następujący plik *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-188">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="b6d5e-189">Następująca Klasa wiąże się z `"MyConfig"` sekcją konfiguracji i stosuje kilka `DataAnnotations` reguł:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-189">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="b6d5e-190">Poniższy kod wywołuje <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> , aby uzyskać [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , który wiąże się z `MyConfigOptions` klasą i umożliwia `DataAnnotations` weryfikację:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-190">The following code calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an  [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class and enables `DataAnnotations` validation:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="b6d5e-191">Poniższy kod wyświetla wartości konfiguracyjne lub błędy walidacji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-191">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="b6d5e-192">Poniższy kod stosuje bardziej złożoną regułę walidacji przy użyciu delegata:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-192">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="b6d5e-193">IValidateOptions na potrzeby złożonej walidacji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-193">IValidateOptions for complex validation</span></span>

<span data-ttu-id="b6d5e-194">Następująca Klasa implementuje <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-194">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="b6d5e-195">`IValidateOptions`umożliwia przeniesienie kodu weryfikacyjnego z `StartUp` i do klasy.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-195">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="b6d5e-196">Przy użyciu powyższego kodu sprawdzanie poprawności jest włączane w programie `Startup.ConfigureServices` przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-196">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

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

## <a name="options-post-configuration"></a><span data-ttu-id="b6d5e-197">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-197">Options post-configuration</span></span>

<span data-ttu-id="b6d5e-198">Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-198">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="b6d5e-199">Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-199">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b6d5e-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępny do po skonfigurowaniu nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b6d5e-201">Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="b6d5e-202">Dostęp do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b6d5e-202">Accessing options during startup</span></span>

<span data-ttu-id="b6d5e-203"><xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-203"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="b6d5e-204">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-204">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b6d5e-205">Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-205">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="b6d5e-206">Options. ConfigurationExtensions — pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="b6d5e-206">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="b6d5e-207">Pakiet [Microsoft. Extensions. options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) jest niejawnie przywoływany w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-207">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b6d5e-208">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-208">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="b6d5e-209">Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-209">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="b6d5e-210">[Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-210">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="b6d5e-211">[Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-211">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="b6d5e-212">Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-212">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="b6d5e-213">Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-213">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="b6d5e-214">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b6d5e-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b6d5e-215">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b6d5e-215">Prerequisites</span></span>

<span data-ttu-id="b6d5e-216">Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-216">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="b6d5e-217">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-217">Options interfaces</span></span>

<span data-ttu-id="b6d5e-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="b6d5e-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>Program obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="b6d5e-220">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="b6d5e-220">Change notifications</span></span>
* [<span data-ttu-id="b6d5e-221">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="b6d5e-221">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="b6d5e-222">Konfiguracja do ponownego ładowania</span><span class="sxs-lookup"><span data-stu-id="b6d5e-222">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="b6d5e-223">Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="b6d5e-223">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="b6d5e-224">Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpieniu całej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-224">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="b6d5e-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych wystąpień opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="b6d5e-226">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-226">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="b6d5e-227">Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-227">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="b6d5e-228">Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-228">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="b6d5e-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="b6d5e-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-230">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="b6d5e-231">Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-231">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="b6d5e-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="b6d5e-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="b6d5e-234">Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-234">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="b6d5e-235"><xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-235"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="b6d5e-236">Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-236">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="b6d5e-237">Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-237">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="b6d5e-238">Konfiguracja opcji ogólnych</span><span class="sxs-lookup"><span data-stu-id="b6d5e-238">General options configuration</span></span>

<span data-ttu-id="b6d5e-239">Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-239">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="b6d5e-240">Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-240">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="b6d5e-241">Następująca Klasa, `MyOptions` , ma dwie właściwości, `Option1` i `Option2` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-241">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="b6d5e-242">Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-242">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="b6d5e-243">`Option2`ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio (*modele/opcje. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-243">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="b6d5e-244">`MyOptions`Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-244">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="b6d5e-245">Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z programem <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w celu uzyskania dostępu do ustawień (*stron/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-245">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="b6d5e-246">Plik *appSettings. JSON* przykładu Określa wartości dla `option1` i `option2` :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-246">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="b6d5e-247">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-247">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="b6d5e-248">W przypadku używania niestandardowej <xref:System.Configuration.ConfigurationBuilder> konfiguracji opcji do ładowania z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-248">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="b6d5e-249">Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-249">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="b6d5e-250">Skonfiguruj proste opcje z delegatem</span><span class="sxs-lookup"><span data-stu-id="b6d5e-250">Configure simple options with a delegate</span></span>

<span data-ttu-id="b6d5e-251">Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-251">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="b6d5e-252">Użyj delegata, aby ustawić wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-252">Use a delegate to set options values.</span></span> <span data-ttu-id="b6d5e-253">Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy (*models/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-253">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="b6d5e-254">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> zostanie dodana druga usługa do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-254">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b6d5e-255">Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-255">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="b6d5e-256">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-256">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="b6d5e-257">Można dodać wielu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-257">You can add multiple configuration providers.</span></span> <span data-ttu-id="b6d5e-258">Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-258">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="b6d5e-259">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-259">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="b6d5e-260">Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-260">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="b6d5e-261">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w pliku *appSettings. JSON*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-261">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="b6d5e-262">Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-262">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="b6d5e-263">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-263">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="b6d5e-264">Konfiguracja podopcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-264">Suboptions configuration</span></span>

<span data-ttu-id="b6d5e-265">Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-265">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="b6d5e-266">Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-266">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="b6d5e-267">Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-267">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="b6d5e-268">Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-268">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="b6d5e-269">Na przykład `MyOptions.Option1` Właściwość jest powiązana z kluczem `Option1` , który jest odczytywany z `option1` właściwości w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-269">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="b6d5e-270">W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-270">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b6d5e-271">Wiąże `MySubOptions` się z sekcją `subsection` pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-271">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="b6d5e-272">`GetSection`Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-272">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="b6d5e-273">Plik *appSettings. JSON* przykładu definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-273">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="b6d5e-274">`MySubOptions`Klasa definiuje właściwości `SubOption1` i `SubOption2` , aby przechowywać wartości opcji (*modele/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-274">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="b6d5e-275">Metoda modelu strony `OnGet` zwraca ciąg z wartościami opcji (*Pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-275">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="b6d5e-276">Gdy aplikacja jest uruchomiona, `OnGet` Metoda zwraca ciąg pokazujący wartości klasy podopcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-276">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="b6d5e-277">Iniekcja opcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-277">Options injection</span></span>

<span data-ttu-id="b6d5e-278">Iniekcja opcji jest przedstawiana jako przykład 4 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-278">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="b6d5e-279">Wsuń <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-279">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="b6d5e-280">RazorWidok strony lub MVC z [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-280">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="b6d5e-281">Model strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-281">A page or view model.</span></span>

<span data-ttu-id="b6d5e-282">Poniższy przykład z przykładowej aplikacji wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu strony (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-282">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="b6d5e-283">Przykładowa aplikacja pokazuje, jak wstrzyknąć `IOptionsMonitor<MyOptions>` przy użyciu `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-283">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="b6d5e-284">Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-284">When the app is run, the options values are shown in the rendered page:</span></span>

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="b6d5e-286">Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="b6d5e-286">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="b6d5e-287">Ponowne ładowanie danych konfiguracyjnych przy użyciu programu <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-287">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="b6d5e-288">W przypadku korzystania z <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-288">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="b6d5e-289">Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-289">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="b6d5e-290">`IOptionsMonitor`jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-290">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="b6d5e-291">`IOptionsSnapshot`jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie `IOptionsSnapshot<T>` konstruowania obiektu.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-291">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="b6d5e-292">Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-292">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="b6d5e-293">W poniższym przykładzie pokazano, jak nowa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest tworzona po wprowadzeniu zmian *appSettings. JSON* (*Pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-293">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="b6d5e-294">Wiele żądań do zwracanych przez serwer wartości stałych dostarczonych przez plik *appSettings. JSON* do momentu zmiany pliku i ponownego załadowania konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-294">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="b6d5e-295">Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-295">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="b6d5e-296">Zmień wartości w pliku *appSettings. JSON* na `value1_from_json UPDATED` i `200` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-296">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="b6d5e-297">Zapisz plik *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-297">Save the *appsettings.json* file.</span></span> <span data-ttu-id="b6d5e-298">Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-298">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="b6d5e-299">Obsługa nazwanych opcji w programie IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="b6d5e-299">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="b6d5e-300">Pomoc techniczna dotycząca opcji nazwanych w programie <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> jest prezentowana jako przykład 6 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-300">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="b6d5e-301">Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-301">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="b6d5e-302">W aplikacji przykładowej nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która wywołuje [ConfigureNamedOptions \<TOptions> . Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-302">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="b6d5e-303">W nazwanych opcjach jest uwzględniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-303">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="b6d5e-304">Aplikacja Przykładowa uzyskuje dostęp do nazwanych opcji przy użyciu <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-304">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="b6d5e-305">Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-305">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="b6d5e-306">`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-306">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="b6d5e-307">`named_options_2`wartości są podawane przez:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-307">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="b6d5e-308">`named_options_2`Delegat w `ConfigureServices` dla `Option1` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-308">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="b6d5e-309">Wartość domyślna dla `Option2` dostarczonych przez `MyOptions` klasę.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-309">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="b6d5e-310">Skonfiguruj wszystkie opcje przy użyciu metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="b6d5e-310">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="b6d5e-311">Skonfiguruj wszystkie wystąpienia opcji za pomocą <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-311">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="b6d5e-312">Poniższy kod konfiguruje `Option1` wszystkie wystąpienia konfiguracji za pomocą wspólnej wartości.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-312">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="b6d5e-313">Ręcznie Dodaj następujący kod do `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-313">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="b6d5e-314">Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-314">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="b6d5e-315">Wszystkie opcje są nazwanymi wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-315">All options are named instances.</span></span> <span data-ttu-id="b6d5e-316">Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-316">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="b6d5e-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="b6d5e-318">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-318">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="b6d5e-319">`null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-319">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="b6d5e-320">Interfejs API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="b6d5e-320">OptionsBuilder API</span></span>

<span data-ttu-id="b6d5e-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="b6d5e-322">`OptionsBuilder`usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-322">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="b6d5e-323">Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-323">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="b6d5e-324">Korzystanie z usług DI Services w celu konfigurowania opcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-324">Use DI services to configure options</span></span>

<span data-ttu-id="b6d5e-325">Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-325">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="b6d5e-326">Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-326">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="b6d5e-327">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) umożliwiające użycie maksymalnie pięciu usług w celu skonfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="b6d5e-328">Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-328">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="b6d5e-329">Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-329">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="b6d5e-330">Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-330">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="b6d5e-331">Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-331">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="b6d5e-332">Sprawdzanie poprawności opcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-332">Options validation</span></span>

<span data-ttu-id="b6d5e-333">Sprawdzanie poprawności opcji umożliwia sprawdzenie opcji w przypadku skonfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-333">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="b6d5e-334">Wywołaj `Validate` metodę walidacji zwracającą wartość, `true` Jeśli opcje są prawidłowe i `false` Jeśli są nieprawidłowe:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-334">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="b6d5e-335">Poprzedni przykład ustawia nazwane wystąpienie opcji na `optionalOptionsName` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-335">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="b6d5e-336">Domyślne wystąpienie opcji to `Options.DefaultName` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-336">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="b6d5e-337">Walidacja jest uruchamiana po utworzeniu wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-337">Validation runs when the options instance is created.</span></span> <span data-ttu-id="b6d5e-338">Wystąpienie opcji gwarantuje przekazanie walidacji przy pierwszym dostępie do niego.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-338">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b6d5e-339">Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-339">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="b6d5e-340">Na przykład `IOptionsSnapshot` Opcje są tworzone i sprawdzane jednokrotnie dla każdego żądania po pierwszym uzyskaniu dostępu do tych opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-340">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="b6d5e-341">Te `IOptionsSnapshot` Opcje nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania*.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-341">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="b6d5e-342">`Validate`Metoda akceptuje `Func<TOptions, bool>` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-342">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="b6d5e-343">Aby w pełni dostosować walidację, zaimplementuj `IValidateOptions<TOptions>` , która umożliwia:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-343">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="b6d5e-344">Sprawdzanie poprawności wielu typów opcji:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="b6d5e-344">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="b6d5e-345">Walidacja zależna od innego typu opcji:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="b6d5e-345">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="b6d5e-346">`IValidateOptions`weryfikuje</span><span class="sxs-lookup"><span data-stu-id="b6d5e-346">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="b6d5e-347">Konkretne nazwane wystąpienie opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-347">A specific named options instance.</span></span>
* <span data-ttu-id="b6d5e-348">Wszystkie opcje w `name` przypadku `null` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-348">All options when `name` is `null`.</span></span>

<span data-ttu-id="b6d5e-349">Zwróć `ValidateOptionsResult` z implementacji interfejsu:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-349">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="b6d5e-350">Sprawdzanie poprawności opartej na adnotacji danych jest dostępne w pakiecie [Microsoft. Extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) poprzez wywołanie <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody w `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-350">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="b6d5e-351">`Microsoft.Extensions.Options.DataAnnotations`jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-351">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="b6d5e-352">Eager sprawdzanie poprawności (niepowodzenie szybkie przy uruchamianiu) jest rozważane dla przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-352">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="b6d5e-353">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-353">Options post-configuration</span></span>

<span data-ttu-id="b6d5e-354">Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-354">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="b6d5e-355">Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-355">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b6d5e-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępny do po skonfigurowaniu nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b6d5e-357">Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="b6d5e-358">Dostęp do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b6d5e-358">Accessing options during startup</span></span>

<span data-ttu-id="b6d5e-359"><xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-359"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="b6d5e-360">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-360">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b6d5e-361">Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-361">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b6d5e-362">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-362">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="b6d5e-363">Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-363">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="b6d5e-364">[Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-364">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="b6d5e-365">[Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-365">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="b6d5e-366">Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-366">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="b6d5e-367">Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-367">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="b6d5e-368">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b6d5e-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b6d5e-369">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b6d5e-369">Prerequisites</span></span>

<span data-ttu-id="b6d5e-370">Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-370">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="b6d5e-371">Interfejsy opcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-371">Options interfaces</span></span>

<span data-ttu-id="b6d5e-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="b6d5e-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>Program obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="b6d5e-374">Powiadomienia o zmianie</span><span class="sxs-lookup"><span data-stu-id="b6d5e-374">Change notifications</span></span>
* [<span data-ttu-id="b6d5e-375">Nazwane opcje</span><span class="sxs-lookup"><span data-stu-id="b6d5e-375">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="b6d5e-376">Konfiguracja do ponownego ładowania</span><span class="sxs-lookup"><span data-stu-id="b6d5e-376">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="b6d5e-377">Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="b6d5e-377">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="b6d5e-378">Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpieniu całej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-378">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="b6d5e-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych wystąpień opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="b6d5e-380">Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-380">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="b6d5e-381">Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-381">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="b6d5e-382">Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-382">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="b6d5e-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="b6d5e-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-384">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="b6d5e-385">Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-385">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="b6d5e-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="b6d5e-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="b6d5e-388">Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-388">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="b6d5e-389"><xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-389"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="b6d5e-390">Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-390">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="b6d5e-391">Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-391">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="b6d5e-392">Konfiguracja opcji ogólnych</span><span class="sxs-lookup"><span data-stu-id="b6d5e-392">General options configuration</span></span>

<span data-ttu-id="b6d5e-393">Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-393">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="b6d5e-394">Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-394">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="b6d5e-395">Następująca Klasa, `MyOptions` , ma dwie właściwości, `Option1` i `Option2` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-395">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="b6d5e-396">Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-396">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="b6d5e-397">`Option2`ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio (*modele/opcje. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-397">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="b6d5e-398">`MyOptions`Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-398">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="b6d5e-399">Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z programem <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w celu uzyskania dostępu do ustawień (*stron/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-399">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="b6d5e-400">Plik *appSettings. JSON* przykładu Określa wartości dla `option1` i `option2` :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-400">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="b6d5e-401">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-401">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="b6d5e-402">W przypadku używania niestandardowej <xref:System.Configuration.ConfigurationBuilder> konfiguracji opcji do ładowania z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-402">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="b6d5e-403">Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-403">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="b6d5e-404">Skonfiguruj proste opcje z delegatem</span><span class="sxs-lookup"><span data-stu-id="b6d5e-404">Configure simple options with a delegate</span></span>

<span data-ttu-id="b6d5e-405">Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-405">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="b6d5e-406">Użyj delegata, aby ustawić wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-406">Use a delegate to set options values.</span></span> <span data-ttu-id="b6d5e-407">Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy (*models/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-407">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="b6d5e-408">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> zostanie dodana druga usługa do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-408">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b6d5e-409">Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-409">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="b6d5e-410">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-410">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="b6d5e-411">Można dodać wielu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-411">You can add multiple configuration providers.</span></span> <span data-ttu-id="b6d5e-412">Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-412">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="b6d5e-413">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-413">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="b6d5e-414">Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-414">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="b6d5e-415">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w pliku *appSettings. JSON*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-415">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="b6d5e-416">Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-416">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="b6d5e-417">Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-417">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="b6d5e-418">Konfiguracja podopcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-418">Suboptions configuration</span></span>

<span data-ttu-id="b6d5e-419">Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-419">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="b6d5e-420">Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-420">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="b6d5e-421">Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-421">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="b6d5e-422">Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-422">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="b6d5e-423">Na przykład `MyOptions.Option1` Właściwość jest powiązana z kluczem `Option1` , który jest odczytywany z `option1` właściwości w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-423">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="b6d5e-424">W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-424">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b6d5e-425">Wiąże `MySubOptions` się z sekcją `subsection` pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-425">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="b6d5e-426">`GetSection`Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-426">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="b6d5e-427">Plik *appSettings. JSON* przykładu definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-427">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="b6d5e-428">`MySubOptions`Klasa definiuje właściwości `SubOption1` i `SubOption2` , aby przechowywać wartości opcji (*modele/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-428">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="b6d5e-429">Metoda modelu strony `OnGet` zwraca ciąg z wartościami opcji (*Pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-429">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="b6d5e-430">Gdy aplikacja jest uruchomiona, `OnGet` Metoda zwraca ciąg pokazujący wartości klasy podopcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-430">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="b6d5e-431">Opcje udostępniane przez model widoku lub bezpośrednie iniekcja widoku</span><span class="sxs-lookup"><span data-stu-id="b6d5e-431">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="b6d5e-432">Opcje udostępniane przez model widoku lub bezpośrednie wstrzyknięcie widoku są przedstawiane jako przykład 4 w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-432">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="b6d5e-433">Opcje można dostarczyć w modelu widoku lub poprzez wstrzyknięcie <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bezpośrednio do widoku (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-433">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="b6d5e-434">Przykładowa aplikacja pokazuje, jak wstrzyknąć `IOptionsMonitor<MyOptions>` przy użyciu `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-434">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="b6d5e-435">Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-435">When the app is run, the options values are shown in the rendered page:</span></span>

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="b6d5e-437">Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="b6d5e-437">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="b6d5e-438">Ponowne ładowanie danych konfiguracyjnych przy użyciu programu <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-438">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="b6d5e-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>obsługuje opcje ponownego ładowania z minimalnym obciążeniem przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="b6d5e-440">Opcje są obliczane raz dla żądania w przypadku uzyskiwania dostępu do pamięci podręcznej i buforowania jej przez okres istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-440">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="b6d5e-441">W poniższym przykładzie pokazano, jak nowa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest tworzona po wprowadzeniu zmian *appSettings. JSON* (*Pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-441">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="b6d5e-442">Wiele żądań do zwracanych przez serwer wartości stałych dostarczonych przez plik *appSettings. JSON* do momentu zmiany pliku i ponownego załadowania konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-442">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="b6d5e-443">Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b6d5e-443">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="b6d5e-444">Zmień wartości w pliku *appSettings. JSON* na `value1_from_json UPDATED` i `200` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-444">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="b6d5e-445">Zapisz plik *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-445">Save the *appsettings.json* file.</span></span> <span data-ttu-id="b6d5e-446">Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-446">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="b6d5e-447">Obsługa nazwanych opcji w programie IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="b6d5e-447">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="b6d5e-448">Pomoc techniczna dotycząca opcji nazwanych w programie <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> jest prezentowana jako przykład 6 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-448">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="b6d5e-449">Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-449">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="b6d5e-450">W aplikacji przykładowej nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która wywołuje [ConfigureNamedOptions \<TOptions> . Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-450">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="b6d5e-451">W nazwanych opcjach jest uwzględniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-451">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="b6d5e-452">Aplikacja Przykładowa uzyskuje dostęp do nazwanych opcji przy użyciu <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b6d5e-452">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="b6d5e-453">Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-453">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="b6d5e-454">`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-454">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="b6d5e-455">`named_options_2`wartości są podawane przez:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-455">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="b6d5e-456">`named_options_2`Delegat w `ConfigureServices` dla `Option1` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-456">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="b6d5e-457">Wartość domyślna dla `Option2` dostarczonych przez `MyOptions` klasę.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-457">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="b6d5e-458">Skonfiguruj wszystkie opcje przy użyciu metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="b6d5e-458">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="b6d5e-459">Skonfiguruj wszystkie wystąpienia opcji za pomocą <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-459">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="b6d5e-460">Poniższy kod konfiguruje `Option1` wszystkie wystąpienia konfiguracji za pomocą wspólnej wartości.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-460">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="b6d5e-461">Ręcznie Dodaj następujący kod do `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-461">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="b6d5e-462">Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-462">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="b6d5e-463">Wszystkie opcje są nazwanymi wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-463">All options are named instances.</span></span> <span data-ttu-id="b6d5e-464">Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-464">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="b6d5e-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="b6d5e-466">Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-466">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="b6d5e-467">`null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-467">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="b6d5e-468">Interfejs API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="b6d5e-468">OptionsBuilder API</span></span>

<span data-ttu-id="b6d5e-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="b6d5e-470">`OptionsBuilder`usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-470">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="b6d5e-471">Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-471">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="b6d5e-472">Korzystanie z usług DI Services w celu konfigurowania opcji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-472">Use DI services to configure options</span></span>

<span data-ttu-id="b6d5e-473">Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-473">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="b6d5e-474">Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-474">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="b6d5e-475">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) umożliwiające użycie maksymalnie pięciu usług w celu skonfigurowania opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="b6d5e-476">Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-476">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="b6d5e-477">Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-477">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="b6d5e-478">Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="b6d5e-478">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="b6d5e-479">Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-479">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="b6d5e-480">Opcje po konfiguracji</span><span class="sxs-lookup"><span data-stu-id="b6d5e-480">Options post-configuration</span></span>

<span data-ttu-id="b6d5e-481">Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-481">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="b6d5e-482">Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-482">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b6d5e-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępny do po skonfigurowaniu nazwanych opcji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b6d5e-484">Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b6d5e-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="b6d5e-485">Dostęp do opcji podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b6d5e-485">Accessing options during startup</span></span>

<span data-ttu-id="b6d5e-486"><xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-486"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="b6d5e-487">Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b6d5e-487">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b6d5e-488">Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="b6d5e-488">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b6d5e-489">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b6d5e-489">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
