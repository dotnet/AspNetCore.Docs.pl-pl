---
<span data-ttu-id="bdab0-101">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bdab0-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="bdab0-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bdab0-102">'Blazor'</span></span>
- <span data-ttu-id="bdab0-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bdab0-103">'Identity'</span></span>
- <span data-ttu-id="bdab0-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bdab0-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="bdab0-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bdab0-105">'Razor'</span></span>
- <span data-ttu-id="bdab0-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bdab0-106">'SignalR' uid:</span></span> 

---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="bdab0-107">Wykrywanie zmian przy użyciu tokenów zmiany w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdab0-107">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bdab0-108">*Tokenem zmiany* jest ogólnym blokiem konstrukcyjnym, który służy do śledzenia zmian stanu.</span><span class="sxs-lookup"><span data-stu-id="bdab0-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="bdab0-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bdab0-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="bdab0-110">IChangeToken, interfejs</span><span class="sxs-lookup"><span data-stu-id="bdab0-110">IChangeToken interface</span></span>

<span data-ttu-id="bdab0-111"><xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="bdab0-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="bdab0-112">`IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="bdab0-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="bdab0-113">Pakiet NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) jest niejawnie dostarczany do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdab0-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="bdab0-114">`IChangeToken`ma dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="bdab0-114">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="bdab0-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskaż, czy token aktywnie wywołuje wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="bdab0-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="bdab0-116">Jeśli `ActiveChangedCallbacks` jest ustawiona na `false` , wywołanie zwrotne nigdy nie zostanie wywołane, a aplikacja musi sondować `HasChanged` pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="bdab0-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="bdab0-117">Istnieje również możliwość, że token nigdy nie zostanie anulowany, jeśli nie wystąpią żadne zmiany lub zostanie usunięty lub wyłączony odbiornik zmian.</span><span class="sxs-lookup"><span data-stu-id="bdab0-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="bdab0-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>odbiera wartość wskazującą, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="bdab0-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="bdab0-119">`IChangeToken`Interfejs zawiera metodę [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , która rejestruje wywołanie zwrotne, które jest wywoływane, gdy token został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="bdab0-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="bdab0-120">`HasChanged`musi być ustawiona przed wywołaniem wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="bdab0-120">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="bdab0-121">Klasa ChangeToken</span><span class="sxs-lookup"><span data-stu-id="bdab0-121">ChangeToken class</span></span>

<span data-ttu-id="bdab0-122"><xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną służącą do propagowania powiadomień, w których wystąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="bdab0-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="bdab0-123">`ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="bdab0-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="bdab0-124">Pakiet NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) jest niejawnie dostarczany do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdab0-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="bdab0-125">Metoda [ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywoływanie przy każdym zmianie tokenu:</span><span class="sxs-lookup"><span data-stu-id="bdab0-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="bdab0-126">`Func<IChangeToken>`tworzy token.</span><span class="sxs-lookup"><span data-stu-id="bdab0-126">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="bdab0-127">`Action`jest wywoływana, gdy zostanie zmieniony token.</span><span class="sxs-lookup"><span data-stu-id="bdab0-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="bdab0-128">Przeciążenie [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest przesyłany do odbiorcy tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="bdab0-129">`OnChange`Zwraca wartość <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="bdab0-129">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="bdab0-130">Wywołanie <xref:System.IDisposable.Dispose*> uniemożliwia wysłuchanie tokenu w celu wprowadzenia dalszych zmian i zwolnienia zasobów tokenu.</span><span class="sxs-lookup"><span data-stu-id="bdab0-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="bdab0-131">Przykładowe zastosowania tokenów zmiany w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdab0-131">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="bdab0-132">Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian obiektów:</span><span class="sxs-lookup"><span data-stu-id="bdab0-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="bdab0-133">W przypadku monitorowania zmian w plikach <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> Metoda tworzy `IChangeToken` dla określonych plików lub folderów, które mają być monitorowane.</span><span class="sxs-lookup"><span data-stu-id="bdab0-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="bdab0-134">`IChangeToken`można dodać tokeny do wpisów pamięci podręcznej, aby wyzwolić wykluczenia z pamięci podręcznej w przypadku zmiany.</span><span class="sxs-lookup"><span data-stu-id="bdab0-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="bdab0-135">W przypadku `TOptions` zmian Domyślna <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma Przeciążenie, które akceptuje co najmniej jedno <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="bdab0-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="bdab0-136">Każde wystąpienie zwraca wartość, `IChangeToken` Aby zarejestrować zmiany zwrotne powiadomień o zmianach opcji śledzenia.</span><span class="sxs-lookup"><span data-stu-id="bdab0-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="bdab0-137">Monitorowanie zmian konfiguracji</span><span class="sxs-lookup"><span data-stu-id="bdab0-137">Monitor for configuration changes</span></span>

<span data-ttu-id="bdab0-138">Domyślnie szablony ASP.NET Core korzystają z [plików konfiguracji JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Development. JSON*i *appSettings. Production. JSON*) w celu załadowania ustawień konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="bdab0-139">Te pliki są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="bdab0-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="bdab0-140">`reloadOnChange`wskazuje, czy należy ponownie załadować konfigurację w przypadku zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="bdab0-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="bdab0-141">To ustawienie jest dostępne w <xref:Microsoft.Extensions.Hosting.Host> metodzie wygodnej <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="bdab0-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="bdab0-142">Konfiguracja oparta na plikach jest reprezentowana przez <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="bdab0-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="bdab0-143">`FileConfigurationSource`program używa <xref:Microsoft.Extensions.FileProviders.IFileProvider> do monitorowania plików.</span><span class="sxs-lookup"><span data-stu-id="bdab0-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="bdab0-144">Domyślnie `IFileMonitor` jest on dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , który służy <xref:System.IO.FileSystemWatcher> do monitorowania zmian w pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="bdab0-145">Przykładowa aplikacja przedstawia dwie implementacje monitorowania zmian konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="bdab0-146">Jeśli którykolwiek z plików *AppSettings* zostanie zmieniony, obydwie implementacje monitorowania plików wykonują kod niestandardowy, &mdash; a Przykładowa aplikacja zapisuje komunikat w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="bdab0-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="bdab0-147">Plik konfiguracji `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenów dla jednej zmiany pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="bdab0-148">Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz w przypadku wyzwolenia wielu wywołań zwrotnych tokenów, implementacja próbki sprawdza skróty plików.</span><span class="sxs-lookup"><span data-stu-id="bdab0-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="bdab0-149">W przykładzie zastosowano mieszanie plików SHA1.</span><span class="sxs-lookup"><span data-stu-id="bdab0-149">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="bdab0-150">Ponowna próba jest zaimplementowana z wycofywaniem wykładniczym.</span><span class="sxs-lookup"><span data-stu-id="bdab0-150">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="bdab0-151">Ponowienie próby jest możliwe, ponieważ może wystąpić zablokowanie pliku, który tymczasowo uniemożliwia Obliczanie nowego skrótu pliku.</span><span class="sxs-lookup"><span data-stu-id="bdab0-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="bdab0-152">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-152">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="bdab0-153">Prosty token zmiany uruchamiania</span><span class="sxs-lookup"><span data-stu-id="bdab0-153">Simple startup change token</span></span>

<span data-ttu-id="bdab0-154">Zarejestruj `Action` wywołanie zwrotne odbiorcy tokenu dla powiadomień o zmianach w tokenie Załaduj konfigurację.</span><span class="sxs-lookup"><span data-stu-id="bdab0-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="bdab0-155">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="bdab0-155">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="bdab0-156">`config.GetReloadToken()`udostępnia token.</span><span class="sxs-lookup"><span data-stu-id="bdab0-156">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="bdab0-157">Wywołanie zwrotne to `InvokeChanged` Metoda:</span><span class="sxs-lookup"><span data-stu-id="bdab0-157">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="bdab0-158">`state`Wywołanie zwrotne jest używane do przekazywania w `IWebHostEnvironment` , co jest przydatne do określenia poprawnego pliku konfiguracyjnego *AppSettings* do monitorowania (na przykład *appSettings. Plik Development. JSON* w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="bdab0-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="bdab0-159">Skróty plików są używane, aby zapobiec `WriteConsole` uruchamianiu instrukcji wiele razy z powodu wywołania zwrotnego wielu tokenów, gdy plik konfiguracyjny został zmieniony tylko raz.</span><span class="sxs-lookup"><span data-stu-id="bdab0-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="bdab0-160">Ten system działa tak długo, jak działa aplikacja i nie może zostać wyłączona przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bdab0-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="bdab0-161">Monitorowanie zmian konfiguracji jako usługi</span><span class="sxs-lookup"><span data-stu-id="bdab0-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="bdab0-162">Przykład implementuje:</span><span class="sxs-lookup"><span data-stu-id="bdab0-162">The sample implements:</span></span>

* <span data-ttu-id="bdab0-163">Podstawowe monitorowanie tokenów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="bdab0-163">Basic startup token monitoring.</span></span>
* <span data-ttu-id="bdab0-164">Monitorowanie jako usługa.</span><span class="sxs-lookup"><span data-stu-id="bdab0-164">Monitoring as a service.</span></span>
* <span data-ttu-id="bdab0-165">Mechanizm do włączania i wyłączania monitorowania.</span><span class="sxs-lookup"><span data-stu-id="bdab0-165">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="bdab0-166">Przykład ustanawia `IConfigurationMonitor` interfejs.</span><span class="sxs-lookup"><span data-stu-id="bdab0-166">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="bdab0-167">*Rozszerzenia/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-167">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="bdab0-168">Konstruktor zaimplementowanej klasy, `ConfigurationMonitor` rejestruje wywołanie zwrotne dla powiadomień o zmianach:</span><span class="sxs-lookup"><span data-stu-id="bdab0-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="bdab0-169">`config.GetReloadToken()`dostarcza token.</span><span class="sxs-lookup"><span data-stu-id="bdab0-169">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="bdab0-170">`InvokeChanged`jest metodą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="bdab0-170">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="bdab0-171">`state`W tym wystąpieniu jest odwołanie do `IConfigurationMonitor` wystąpienia, które jest używane w celu uzyskania dostępu do stanu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="bdab0-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="bdab0-172">Są używane dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="bdab0-172">Two properties are used:</span></span>

* <span data-ttu-id="bdab0-173">`MonitoringEnabled`: Wskazuje, czy wywołanie zwrotne powinno uruchamiać swój kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="bdab0-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="bdab0-174">`CurrentState`: Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bdab0-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="bdab0-175">`InvokeChanged`Metoda jest podobna do wcześniejszego podejścia, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="bdab0-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="bdab0-176">Kod nie jest uruchamiany, chyba że `MonitoringEnabled` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="bdab0-177">Wyprowadza bieżącą wartość `state` w jej `WriteConsole` danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="bdab0-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="bdab0-178">Wystąpienie `ConfigurationMonitor` jest zarejestrowane jako usługa w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bdab0-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="bdab0-179">Strona indeks zapewnia kontrolę nad konfiguracją przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bdab0-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="bdab0-180">Wystąpienie `IConfigurationMonitor` jest wstrzykiwane do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="bdab0-181">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-181">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="bdab0-182">Monitor konfiguracji ( `_monitor` ) służy do włączania lub wyłączania monitorowania oraz ustawiania bieżącego stanu dla opinii o interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bdab0-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="bdab0-183">Gdy `OnPostStartMonitoring` jest wyzwalane, monitorowanie jest włączone, a bieżący stan jest wyczyszczony.</span><span class="sxs-lookup"><span data-stu-id="bdab0-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="bdab0-184">Gdy `OnPostStopMonitoring` jest wyzwalane, monitorowanie jest wyłączone, a stan jest ustawiony na odzwierciedlenie tego, że monitorowanie nie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="bdab0-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="bdab0-185">Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.</span><span class="sxs-lookup"><span data-stu-id="bdab0-185">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="bdab0-186">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-186">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="bdab0-187">Monitoruj zmiany plików w pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="bdab0-187">Monitor cached file changes</span></span>

<span data-ttu-id="bdab0-188">Zawartość pliku może być buforowana w pamięci za pomocą polecenia <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="bdab0-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="bdab0-189">Buforowanie w pamięci jest opisane w temacie [pamięć podręczna w pamięci podręcznej](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="bdab0-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="bdab0-190">Bez podejmowania dodatkowych czynności, takich jak implementacja opisana poniżej, *nieodświeżone (przestarzałe* ) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe zostaną zmienione.</span><span class="sxs-lookup"><span data-stu-id="bdab0-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="bdab0-191">Na przykład nie uwzględnia stanu pliku źródłowego w pamięci podręcznej podczas odnawiania przedziału czasu [wygaśnięcia](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="bdab0-192">Każde żądanie dotyczące danych odnawia przedział czasu wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="bdab0-193">Wszystkie funkcje aplikacji korzystające z zawartości w pamięci podręcznej są uzależnione od potencjalnie otrzymywania nieodświeżonej zawartości.</span><span class="sxs-lookup"><span data-stu-id="bdab0-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="bdab0-194">Użycie tokenów zmiany w scenariuszu buforowania plików uniemożliwia obecność przestarzałych zawartości plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="bdab0-195">Przykładowa aplikacja prezentuje implementację metody.</span><span class="sxs-lookup"><span data-stu-id="bdab0-195">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="bdab0-196">Przykład używa `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="bdab0-196">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="bdab0-197">Zwróć zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="bdab0-197">Return file content.</span></span>
* <span data-ttu-id="bdab0-198">Zaimplementuj algorytm ponawiania prób z wycofywaniem z powrotem, aby uwzględnić przypadki, w których blokada pliku tymczasowo uniemożliwia odczytywanie pliku.</span><span class="sxs-lookup"><span data-stu-id="bdab0-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="bdab0-199">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-199">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="bdab0-200">`FileService`Jest tworzony w celu obsługi wyszukiwania plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="bdab0-201">`GetFileContent`Wywołanie metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci i zwrócić ją do obiektu wywołującego (*Services/FileService. cs*).</span><span class="sxs-lookup"><span data-stu-id="bdab0-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="bdab0-202">Jeśli buforowana zawartość nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną wykonane następujące akcje:</span><span class="sxs-lookup"><span data-stu-id="bdab0-202">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="bdab0-203">Zawartość pliku jest uzyskiwana przy użyciu `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-203">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="bdab0-204">Token zmiany jest uzyskiwany od dostawcy plików z [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="bdab0-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="bdab0-205">Wywołanie zwrotne tokenu jest wyzwalane, gdy plik zostanie zmodyfikowany.</span><span class="sxs-lookup"><span data-stu-id="bdab0-205">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="bdab0-206">Zawartość pliku jest buforowana z [ruchomym](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) okresem ważności.</span><span class="sxs-lookup"><span data-stu-id="bdab0-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="bdab0-207">Token zmiany jest dołączony do [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) w celu wykluczenia wpisu pamięci podręcznej, jeśli plik zostanie zmieniony w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="bdab0-208">W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="bdab0-209">`IWebHostEnvironment.ContentRootFileProvider`służy do uzyskania <xref:Microsoft.Extensions.FileProviders.IFileProvider> wskazujący na aplikację `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="bdab0-210">`filePath`Jest on uzyskiwany za pomocą [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="bdab0-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="bdab0-211">`FileService`Program jest zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.</span><span class="sxs-lookup"><span data-stu-id="bdab0-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="bdab0-212">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bdab0-212">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="bdab0-213">Model strony ładuje zawartość pliku przy użyciu usługi.</span><span class="sxs-lookup"><span data-stu-id="bdab0-213">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="bdab0-214">Na stronie indeksu `OnGet` (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="bdab0-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="bdab0-215">Klasa CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="bdab0-215">CompositeChangeToken class</span></span>

<span data-ttu-id="bdab0-216">Dla reprezentowania jednego lub większej liczby `IChangeToken` wystąpień w pojedynczym obiekcie, użyj <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> klasy.</span><span class="sxs-lookup"><span data-stu-id="bdab0-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="bdab0-217">`HasChanged`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `HasChanged` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="bdab0-218">`ActiveChangeCallbacks`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `ActiveChangeCallbacks` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="bdab0-219">W przypadku wystąpienia wielu współbieżnych zdarzeń zmiany wywołanie zwrotne zmiany złożonego jest wywoływana jednokrotnie.</span><span class="sxs-lookup"><span data-stu-id="bdab0-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bdab0-220">*Tokenem zmiany* jest ogólnym blokiem konstrukcyjnym, który służy do śledzenia zmian stanu.</span><span class="sxs-lookup"><span data-stu-id="bdab0-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="bdab0-221">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bdab0-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="bdab0-222">IChangeToken, interfejs</span><span class="sxs-lookup"><span data-stu-id="bdab0-222">IChangeToken interface</span></span>

<span data-ttu-id="bdab0-223"><xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="bdab0-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="bdab0-224">`IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="bdab0-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="bdab0-225">W przypadku aplikacji, które nie korzystają z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu dla pakietu NuGet [Microsoft. Extensions. pierwotne](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="bdab0-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="bdab0-226">`IChangeToken`ma dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="bdab0-226">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="bdab0-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskaż, czy token aktywnie wywołuje wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="bdab0-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="bdab0-228">Jeśli `ActiveChangedCallbacks` jest ustawiona na `false` , wywołanie zwrotne nigdy nie zostanie wywołane, a aplikacja musi sondować `HasChanged` pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="bdab0-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="bdab0-229">Istnieje również możliwość, że token nigdy nie zostanie anulowany, jeśli nie wystąpią żadne zmiany lub zostanie usunięty lub wyłączony odbiornik zmian.</span><span class="sxs-lookup"><span data-stu-id="bdab0-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="bdab0-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>odbiera wartość wskazującą, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="bdab0-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="bdab0-231">`IChangeToken`Interfejs zawiera metodę [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , która rejestruje wywołanie zwrotne, które jest wywoływane, gdy token został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="bdab0-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="bdab0-232">`HasChanged`musi być ustawiona przed wywołaniem wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="bdab0-232">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="bdab0-233">Klasa ChangeToken</span><span class="sxs-lookup"><span data-stu-id="bdab0-233">ChangeToken class</span></span>

<span data-ttu-id="bdab0-234"><xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną służącą do propagowania powiadomień, w których wystąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="bdab0-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="bdab0-235">`ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="bdab0-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="bdab0-236">W przypadku aplikacji, które nie korzystają z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu dla pakietu NuGet [Microsoft. Extensions. pierwotne](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="bdab0-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="bdab0-237">Metoda [ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywoływanie przy każdym zmianie tokenu:</span><span class="sxs-lookup"><span data-stu-id="bdab0-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="bdab0-238">`Func<IChangeToken>`tworzy token.</span><span class="sxs-lookup"><span data-stu-id="bdab0-238">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="bdab0-239">`Action`jest wywoływana, gdy zostanie zmieniony token.</span><span class="sxs-lookup"><span data-stu-id="bdab0-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="bdab0-240">Przeciążenie [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest przesyłany do odbiorcy tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="bdab0-241">`OnChange`Zwraca wartość <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="bdab0-241">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="bdab0-242">Wywołanie <xref:System.IDisposable.Dispose*> uniemożliwia wysłuchanie tokenu w celu wprowadzenia dalszych zmian i zwolnienia zasobów tokenu.</span><span class="sxs-lookup"><span data-stu-id="bdab0-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="bdab0-243">Przykładowe zastosowania tokenów zmiany w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdab0-243">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="bdab0-244">Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian obiektów:</span><span class="sxs-lookup"><span data-stu-id="bdab0-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="bdab0-245">W przypadku monitorowania zmian w plikach <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> Metoda tworzy `IChangeToken` dla określonych plików lub folderów, które mają być monitorowane.</span><span class="sxs-lookup"><span data-stu-id="bdab0-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="bdab0-246">`IChangeToken`można dodać tokeny do wpisów pamięci podręcznej, aby wyzwolić wykluczenia z pamięci podręcznej w przypadku zmiany.</span><span class="sxs-lookup"><span data-stu-id="bdab0-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="bdab0-247">W przypadku `TOptions` zmian Domyślna <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma Przeciążenie, które akceptuje co najmniej jedno <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="bdab0-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="bdab0-248">Każde wystąpienie zwraca wartość, `IChangeToken` Aby zarejestrować zmiany zwrotne powiadomień o zmianach opcji śledzenia.</span><span class="sxs-lookup"><span data-stu-id="bdab0-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="bdab0-249">Monitorowanie zmian konfiguracji</span><span class="sxs-lookup"><span data-stu-id="bdab0-249">Monitor for configuration changes</span></span>

<span data-ttu-id="bdab0-250">Domyślnie szablony ASP.NET Core korzystają z [plików konfiguracji JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Development. JSON*i *appSettings. Production. JSON*) w celu załadowania ustawień konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="bdab0-251">Te pliki są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="bdab0-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="bdab0-252">`reloadOnChange`wskazuje, czy należy ponownie załadować konfigurację w przypadku zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="bdab0-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="bdab0-253">To ustawienie jest dostępne w <xref:Microsoft.AspNetCore.WebHost> metodzie wygodnej <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="bdab0-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="bdab0-254">Konfiguracja oparta na plikach jest reprezentowana przez <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="bdab0-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="bdab0-255">`FileConfigurationSource`program używa <xref:Microsoft.Extensions.FileProviders.IFileProvider> do monitorowania plików.</span><span class="sxs-lookup"><span data-stu-id="bdab0-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="bdab0-256">Domyślnie `IFileMonitor` jest on dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , który służy <xref:System.IO.FileSystemWatcher> do monitorowania zmian w pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="bdab0-257">Przykładowa aplikacja przedstawia dwie implementacje monitorowania zmian konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="bdab0-258">Jeśli którykolwiek z plików *AppSettings* zostanie zmieniony, obydwie implementacje monitorowania plików wykonują kod niestandardowy, &mdash; a Przykładowa aplikacja zapisuje komunikat w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="bdab0-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="bdab0-259">Plik konfiguracji `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenów dla jednej zmiany pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="bdab0-260">Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz w przypadku wyzwolenia wielu wywołań zwrotnych tokenów, implementacja próbki sprawdza skróty plików.</span><span class="sxs-lookup"><span data-stu-id="bdab0-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="bdab0-261">W przykładzie zastosowano mieszanie plików SHA1.</span><span class="sxs-lookup"><span data-stu-id="bdab0-261">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="bdab0-262">Ponowna próba jest zaimplementowana z wycofywaniem wykładniczym.</span><span class="sxs-lookup"><span data-stu-id="bdab0-262">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="bdab0-263">Ponowienie próby jest możliwe, ponieważ może wystąpić zablokowanie pliku, który tymczasowo uniemożliwia Obliczanie nowego skrótu pliku.</span><span class="sxs-lookup"><span data-stu-id="bdab0-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="bdab0-264">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-264">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="bdab0-265">Prosty token zmiany uruchamiania</span><span class="sxs-lookup"><span data-stu-id="bdab0-265">Simple startup change token</span></span>

<span data-ttu-id="bdab0-266">Zarejestruj `Action` wywołanie zwrotne odbiorcy tokenu dla powiadomień o zmianach w tokenie Załaduj konfigurację.</span><span class="sxs-lookup"><span data-stu-id="bdab0-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="bdab0-267">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="bdab0-267">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="bdab0-268">`config.GetReloadToken()`udostępnia token.</span><span class="sxs-lookup"><span data-stu-id="bdab0-268">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="bdab0-269">Wywołanie zwrotne to `InvokeChanged` Metoda:</span><span class="sxs-lookup"><span data-stu-id="bdab0-269">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="bdab0-270">`state`Wywołanie zwrotne jest używane do przekazywania w `IHostingEnvironment` , co jest przydatne do określenia poprawnego pliku konfiguracyjnego *AppSettings* do monitorowania (na przykład *appSettings. Plik Development. JSON* w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="bdab0-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="bdab0-271">Skróty plików są używane, aby zapobiec `WriteConsole` uruchamianiu instrukcji wiele razy z powodu wywołania zwrotnego wielu tokenów, gdy plik konfiguracyjny został zmieniony tylko raz.</span><span class="sxs-lookup"><span data-stu-id="bdab0-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="bdab0-272">Ten system działa tak długo, jak działa aplikacja i nie może zostać wyłączona przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bdab0-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="bdab0-273">Monitorowanie zmian konfiguracji jako usługi</span><span class="sxs-lookup"><span data-stu-id="bdab0-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="bdab0-274">Przykład implementuje:</span><span class="sxs-lookup"><span data-stu-id="bdab0-274">The sample implements:</span></span>

* <span data-ttu-id="bdab0-275">Podstawowe monitorowanie tokenów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="bdab0-275">Basic startup token monitoring.</span></span>
* <span data-ttu-id="bdab0-276">Monitorowanie jako usługa.</span><span class="sxs-lookup"><span data-stu-id="bdab0-276">Monitoring as a service.</span></span>
* <span data-ttu-id="bdab0-277">Mechanizm do włączania i wyłączania monitorowania.</span><span class="sxs-lookup"><span data-stu-id="bdab0-277">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="bdab0-278">Przykład ustanawia `IConfigurationMonitor` interfejs.</span><span class="sxs-lookup"><span data-stu-id="bdab0-278">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="bdab0-279">*Rozszerzenia/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-279">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="bdab0-280">Konstruktor zaimplementowanej klasy, `ConfigurationMonitor` rejestruje wywołanie zwrotne dla powiadomień o zmianach:</span><span class="sxs-lookup"><span data-stu-id="bdab0-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="bdab0-281">`config.GetReloadToken()`dostarcza token.</span><span class="sxs-lookup"><span data-stu-id="bdab0-281">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="bdab0-282">`InvokeChanged`jest metodą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="bdab0-282">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="bdab0-283">`state`W tym wystąpieniu jest odwołanie do `IConfigurationMonitor` wystąpienia, które jest używane w celu uzyskania dostępu do stanu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="bdab0-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="bdab0-284">Są używane dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="bdab0-284">Two properties are used:</span></span>

* <span data-ttu-id="bdab0-285">`MonitoringEnabled`: Wskazuje, czy wywołanie zwrotne powinno uruchamiać swój kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="bdab0-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="bdab0-286">`CurrentState`: Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bdab0-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="bdab0-287">`InvokeChanged`Metoda jest podobna do wcześniejszego podejścia, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="bdab0-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="bdab0-288">Kod nie jest uruchamiany, chyba że `MonitoringEnabled` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="bdab0-289">Wyprowadza bieżącą wartość `state` w jej `WriteConsole` danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="bdab0-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="bdab0-290">Wystąpienie `ConfigurationMonitor` jest zarejestrowane jako usługa w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bdab0-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="bdab0-291">Strona indeks zapewnia kontrolę nad konfiguracją przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bdab0-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="bdab0-292">Wystąpienie `IConfigurationMonitor` jest wstrzykiwane do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="bdab0-293">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-293">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="bdab0-294">Monitor konfiguracji ( `_monitor` ) służy do włączania lub wyłączania monitorowania oraz ustawiania bieżącego stanu dla opinii o interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bdab0-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="bdab0-295">Gdy `OnPostStartMonitoring` jest wyzwalane, monitorowanie jest włączone, a bieżący stan jest wyczyszczony.</span><span class="sxs-lookup"><span data-stu-id="bdab0-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="bdab0-296">Gdy `OnPostStopMonitoring` jest wyzwalane, monitorowanie jest wyłączone, a stan jest ustawiony na odzwierciedlenie tego, że monitorowanie nie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="bdab0-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="bdab0-297">Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.</span><span class="sxs-lookup"><span data-stu-id="bdab0-297">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="bdab0-298">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-298">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="bdab0-299">Monitoruj zmiany plików w pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="bdab0-299">Monitor cached file changes</span></span>

<span data-ttu-id="bdab0-300">Zawartość pliku może być buforowana w pamięci za pomocą polecenia <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="bdab0-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="bdab0-301">Buforowanie w pamięci jest opisane w temacie [pamięć podręczna w pamięci podręcznej](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="bdab0-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="bdab0-302">Bez podejmowania dodatkowych czynności, takich jak implementacja opisana poniżej, *nieodświeżone (przestarzałe* ) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe zostaną zmienione.</span><span class="sxs-lookup"><span data-stu-id="bdab0-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="bdab0-303">Na przykład nie uwzględnia stanu pliku źródłowego w pamięci podręcznej podczas odnawiania przedziału czasu [wygaśnięcia](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="bdab0-304">Każde żądanie dotyczące danych odnawia przedział czasu wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="bdab0-305">Wszystkie funkcje aplikacji korzystające z zawartości w pamięci podręcznej są uzależnione od potencjalnie otrzymywania nieodświeżonej zawartości.</span><span class="sxs-lookup"><span data-stu-id="bdab0-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="bdab0-306">Użycie tokenów zmiany w scenariuszu buforowania plików uniemożliwia obecność przestarzałych zawartości plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="bdab0-307">Przykładowa aplikacja prezentuje implementację metody.</span><span class="sxs-lookup"><span data-stu-id="bdab0-307">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="bdab0-308">Przykład używa `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="bdab0-308">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="bdab0-309">Zwróć zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="bdab0-309">Return file content.</span></span>
* <span data-ttu-id="bdab0-310">Zaimplementuj algorytm ponawiania prób z wycofywaniem z powrotem, aby uwzględnić przypadki, w których blokada pliku tymczasowo uniemożliwia odczytywanie pliku.</span><span class="sxs-lookup"><span data-stu-id="bdab0-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="bdab0-311">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="bdab0-311">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="bdab0-312">`FileService`Jest tworzony w celu obsługi wyszukiwania plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="bdab0-313">`GetFileContent`Wywołanie metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci i zwrócić ją do obiektu wywołującego (*Services/FileService. cs*).</span><span class="sxs-lookup"><span data-stu-id="bdab0-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="bdab0-314">Jeśli buforowana zawartość nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną wykonane następujące akcje:</span><span class="sxs-lookup"><span data-stu-id="bdab0-314">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="bdab0-315">Zawartość pliku jest uzyskiwana przy użyciu `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-315">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="bdab0-316">Token zmiany jest uzyskiwany od dostawcy plików z [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="bdab0-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="bdab0-317">Wywołanie zwrotne tokenu jest wyzwalane, gdy plik zostanie zmodyfikowany.</span><span class="sxs-lookup"><span data-stu-id="bdab0-317">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="bdab0-318">Zawartość pliku jest buforowana z [ruchomym](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) okresem ważności.</span><span class="sxs-lookup"><span data-stu-id="bdab0-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="bdab0-319">Token zmiany jest dołączony do [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) w celu wykluczenia wpisu pamięci podręcznej, jeśli plik zostanie zmieniony w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="bdab0-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="bdab0-320">W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bdab0-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="bdab0-321">[IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) służy do uzyskania <xref:Microsoft.Extensions.FileProviders.IFileProvider> wskazania wskazujące na aplikację <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> .</span><span class="sxs-lookup"><span data-stu-id="bdab0-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="bdab0-322">`filePath`Jest on uzyskiwany za pomocą [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="bdab0-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="bdab0-323">`FileService`Program jest zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.</span><span class="sxs-lookup"><span data-stu-id="bdab0-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="bdab0-324">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bdab0-324">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="bdab0-325">Model strony ładuje zawartość pliku przy użyciu usługi.</span><span class="sxs-lookup"><span data-stu-id="bdab0-325">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="bdab0-326">Na stronie indeksu `OnGet` (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="bdab0-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="bdab0-327">Klasa CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="bdab0-327">CompositeChangeToken class</span></span>

<span data-ttu-id="bdab0-328">Dla reprezentowania jednego lub większej liczby `IChangeToken` wystąpień w pojedynczym obiekcie, użyj <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> klasy.</span><span class="sxs-lookup"><span data-stu-id="bdab0-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="bdab0-329">`HasChanged`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `HasChanged` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-329">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="bdab0-330">`ActiveChangeCallbacks`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `ActiveChangeCallbacks` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="bdab0-330">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="bdab0-331">W przypadku wystąpienia wielu współbieżnych zdarzeń zmiany wywołanie zwrotne zmiany złożonego jest wywoływana jednokrotnie.</span><span class="sxs-lookup"><span data-stu-id="bdab0-331">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="bdab0-332">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bdab0-332">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
