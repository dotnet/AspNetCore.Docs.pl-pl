---
title: Wykrywanie zmian za pomocą tokenów zmian w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać tokenów zmian do śledzenia zmian.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 70451e219f1295b854e2f84aac55f0cfd1786b19
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656346"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="481df-103">Wykrywanie zmian za pomocą tokenów zmian w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="481df-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="481df-104">*Token zmiany* jest uniwersalnym, niskopoziomowym blokiem konstrukcyjnym używanym do śledzenia zmian stanu.</span><span class="sxs-lookup"><span data-stu-id="481df-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="481df-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="481df-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="481df-106">Interfejs IChangeToken</span><span class="sxs-lookup"><span data-stu-id="481df-106">IChangeToken interface</span></span>

<span data-ttu-id="481df-107"><xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="481df-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="481df-108">`IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> obszarze nazw.</span><span class="sxs-lookup"><span data-stu-id="481df-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="481df-109">[Pakiet Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet jest niejawnie dostarczany do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="481df-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="481df-110">`IChangeToken`posiada dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="481df-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="481df-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskazać, czy token proaktywnie wywołuje wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="481df-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="481df-112">Jeśli `ActiveChangedCallbacks` jest `false`ustawiona na , wywołanie zwrotne `HasChanged` nigdy nie jest wywoływane, a aplikacja musi sondować zmiany.</span><span class="sxs-lookup"><span data-stu-id="481df-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="481df-113">Jest również możliwe dla tokenu nigdy nie zostać anulowane, jeśli nie nastąpią żadne zmiany lub odbiornika zmian podstawowych jest usuwany lub wyłączony.</span><span class="sxs-lookup"><span data-stu-id="481df-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="481df-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>otrzymuje wartość, która wskazuje, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="481df-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="481df-115">Interfejs `IChangeToken` zawiera [RegisterChangeCallback(Action\<Object>, Object),](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) który rejestruje wywołanie zwrotne, który jest wywoływany po zmianie tokenu.</span><span class="sxs-lookup"><span data-stu-id="481df-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="481df-116">`HasChanged`należy ustawić przed wywołaniem wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="481df-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="481df-117">ChangeToken, klasa</span><span class="sxs-lookup"><span data-stu-id="481df-117">ChangeToken class</span></span>

<span data-ttu-id="481df-118"><xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną używaną do propagowania powiadomień o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="481df-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="481df-119">`ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> obszarze nazw.</span><span class="sxs-lookup"><span data-stu-id="481df-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="481df-120">[Pakiet Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet jest niejawnie dostarczany do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="481df-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="481df-121">[ChangeToken.OnChange(Func\<IChangeToken>, akcja)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywołać do każdej zmiany tokenu:</span><span class="sxs-lookup"><span data-stu-id="481df-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="481df-122">`Func<IChangeToken>`tworzy żeton.</span><span class="sxs-lookup"><span data-stu-id="481df-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="481df-123">`Action`jest wywoływana, gdy zmienia się token.</span><span class="sxs-lookup"><span data-stu-id="481df-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="481df-124">Przeciążenie [\<ChangeToken.OnChange TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest `Action`przekazywany do konsumenta tokenu .</span><span class="sxs-lookup"><span data-stu-id="481df-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="481df-125">`OnChange`zwraca <xref:System.IDisposable>plik .</span><span class="sxs-lookup"><span data-stu-id="481df-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="481df-126">Wywołanie <xref:System.IDisposable.Dispose*> zatrzymuje tokenu od nasłuchiwania dalszych zmian i zwalnia zasoby tokenu.</span><span class="sxs-lookup"><span data-stu-id="481df-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="481df-127">Przykładowe zastosowania tokenów zmian w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="481df-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="481df-128">Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian w obiektach:</span><span class="sxs-lookup"><span data-stu-id="481df-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="481df-129">Do monitorowania zmian <xref:Microsoft.Extensions.FileProviders.IFileProvider>w <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> plikach, `IChangeToken` 's metoda tworzy dla określonych plików lub folderu do oglądania.</span><span class="sxs-lookup"><span data-stu-id="481df-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="481df-130">`IChangeToken`tokeny mogą być dodawane do wpisów pamięci podręcznej, aby wyzwolić eksmisje pamięci podręcznej na zmiany.</span><span class="sxs-lookup"><span data-stu-id="481df-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="481df-131">W `TOptions` przypadku zmian <xref:Microsoft.Extensions.Options.OptionsMonitor`1> domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma przeciążenie, które akceptuje jedno lub więcej <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="481df-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="481df-132">Każde wystąpienie `IChangeToken` zwraca, aby zarejestrować wywołanie zwrotne powiadomienia o zmianie dla zmian opcji śledzenia.</span><span class="sxs-lookup"><span data-stu-id="481df-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="481df-133">Monitoruj zmiany konfiguracji</span><span class="sxs-lookup"><span data-stu-id="481df-133">Monitor for configuration changes</span></span>

<span data-ttu-id="481df-134">Domyślnie ASP.NET szablonów Core używają [plików konfiguracyjnych JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.json*i *appsettings. Production.json*), aby załadować ustawienia konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="481df-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="481df-135">Pliki te są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean),](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="481df-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="481df-136">`reloadOnChange`wskazuje, czy konfiguracja powinna zostać ponownie załadowana przy zmianach w pliku.</span><span class="sxs-lookup"><span data-stu-id="481df-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="481df-137">To ustawienie pojawia <xref:Microsoft.Extensions.Hosting.Host> się <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>w metodzie wygody:</span><span class="sxs-lookup"><span data-stu-id="481df-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="481df-138">Konfiguracja oparta na <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>plikach jest reprezentowana przez program .</span><span class="sxs-lookup"><span data-stu-id="481df-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="481df-139">`FileConfigurationSource`do <xref:Microsoft.Extensions.FileProviders.IFileProvider> monitorowania plików.</span><span class="sxs-lookup"><span data-stu-id="481df-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="481df-140">Domyślnie `IFileMonitor` jest dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>program , <xref:System.IO.FileSystemWatcher> który służy do monitorowania zmian w pliku konfiguracyjnym.</span><span class="sxs-lookup"><span data-stu-id="481df-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="481df-141">Przykładowa aplikacja demonstruje dwie implementacje do monitorowania zmian konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="481df-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="481df-142">Jeśli którykolwiek z *appsettings* plików zmiany, zarówno implementacje monitorowania plików wykonać kod&mdash;niestandardowy przykładowy aplikacja zapisuje komunikat do konsoli.</span><span class="sxs-lookup"><span data-stu-id="481df-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="481df-143">Plik konfiguracyjny `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenu dla zmiany pliku pojedynczej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="481df-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="481df-144">Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz po wyzwoleniu wielu wywołań zwrotnych tokenu, implementacja przykładu sprawdza skróty plików.</span><span class="sxs-lookup"><span data-stu-id="481df-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="481df-145">W przykładzie użyto mieszania plików SHA1.</span><span class="sxs-lookup"><span data-stu-id="481df-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="481df-146">Ponowna próba jest implementowana z wykładniczym wycofywania.</span><span class="sxs-lookup"><span data-stu-id="481df-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="481df-147">Ponowna próby jest obecny, ponieważ może wystąpić blokowanie plików, które tymczasowo uniemożliwia obliczanie nowego skrótu w pliku.</span><span class="sxs-lookup"><span data-stu-id="481df-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="481df-148">*Narzędzia/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="481df-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="481df-149">Prosty token zmiany uruchamiania</span><span class="sxs-lookup"><span data-stu-id="481df-149">Simple startup change token</span></span>

<span data-ttu-id="481df-150">Zarejestruj wywołania `Action` zwrotne konsumenta tokenu dla powiadomień o zmianie do tokenu ponownego ładowania konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="481df-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="481df-151">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="481df-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="481df-152">`config.GetReloadToken()`zapewnia token.</span><span class="sxs-lookup"><span data-stu-id="481df-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="481df-153">Wywołanie zwrotne `InvokeChanged` jest metodą:</span><span class="sxs-lookup"><span data-stu-id="481df-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="481df-154">Wywołanie zwrotne jest używany do przekazywania `state` w `IWebHostEnvironment`programie , który jest przydatny do określenia pliku konfiguracji poprawne *appsettings* do monitorowania (na przykład *appsettings. Development.json,* gdy w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="481df-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="481df-155">Skróty plików są używane, `WriteConsole` aby zapobiec wiele razy uruchamianie instrukcji z powodu wielu wywołań zwrotnych tokenu, gdy plik konfiguracji zmienił się tylko raz.</span><span class="sxs-lookup"><span data-stu-id="481df-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="481df-156">Ten system działa tak długo, jak aplikacja jest uruchomiona i nie może być wyłączona przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="481df-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="481df-157">Monitorowanie zmian konfiguracji jako usługi</span><span class="sxs-lookup"><span data-stu-id="481df-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="481df-158">Przykład implementuje:</span><span class="sxs-lookup"><span data-stu-id="481df-158">The sample implements:</span></span>

* <span data-ttu-id="481df-159">Podstawowe monitorowanie tokenu uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="481df-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="481df-160">Monitorowanie jako usługa.</span><span class="sxs-lookup"><span data-stu-id="481df-160">Monitoring as a service.</span></span>
* <span data-ttu-id="481df-161">Mechanizm włączania i wyłączania monitorowania.</span><span class="sxs-lookup"><span data-stu-id="481df-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="481df-162">Próbka ustanawia `IConfigurationMonitor` interfejs.</span><span class="sxs-lookup"><span data-stu-id="481df-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="481df-163">*Rozszerzenia/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="481df-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="481df-164">Konstruktor zaimplementowana `ConfigurationMonitor`klasa, rejestruje wywołanie zwrotne dla powiadomień o zmianie:</span><span class="sxs-lookup"><span data-stu-id="481df-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="481df-165">`config.GetReloadToken()`dostarcza żeton.</span><span class="sxs-lookup"><span data-stu-id="481df-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="481df-166">`InvokeChanged`jest metodą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="481df-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="481df-167">W `state` tym przypadku jest odwołanie `IConfigurationMonitor` do wystąpienia, który jest używany do dostępu do stanu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="481df-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="481df-168">Używane są dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="481df-168">Two properties are used:</span></span>

* <span data-ttu-id="481df-169">`MonitoringEnabled`&ndash; Wskazuje, czy wywołanie zwrotne należy uruchomić jego kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="481df-169">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="481df-170">`CurrentState`&ndash; Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="481df-170">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="481df-171">Metoda `InvokeChanged` jest podobna do wcześniejszego podejścia, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="481df-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="481df-172">Nie uruchamia swojego kodu, chyba że `MonitoringEnabled` jest `true`.</span><span class="sxs-lookup"><span data-stu-id="481df-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="481df-173">Wyprowadza prąd `state` w `WriteConsole` jego wyjściu.</span><span class="sxs-lookup"><span data-stu-id="481df-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="481df-174">Wystąpienie `ConfigurationMonitor` jest rejestrowane jako `Startup.ConfigureServices`usługa w:</span><span class="sxs-lookup"><span data-stu-id="481df-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="481df-175">Strona Indeks oferuje kontrolę użytkownika nad monitorowaniem konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="481df-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="481df-176">Wystąpienie `IConfigurationMonitor` jest wstrzykiwane `IndexModel`do pliku .</span><span class="sxs-lookup"><span data-stu-id="481df-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="481df-177">*Strony/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="481df-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="481df-178">Monitor konfiguracji`_monitor`( ) służy do włączania lub wyłączania monitorowania i ustawiania bieżącego stanu sprzężenia zwrotnego interfejsu użytkownika:</span><span class="sxs-lookup"><span data-stu-id="481df-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="481df-179">Po `OnPostStartMonitoring` wyzwoleniu monitorowanie jest włączone, a bieżący stan jest czyszczony.</span><span class="sxs-lookup"><span data-stu-id="481df-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="481df-180">Po `OnPostStopMonitoring` wyzwoleniu monitorowanie jest wyłączone, a stan jest ustawiony tak, aby odzwierciedlać, że monitorowanie nie występuje.</span><span class="sxs-lookup"><span data-stu-id="481df-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="481df-181">Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.</span><span class="sxs-lookup"><span data-stu-id="481df-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="481df-182">*Strony/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="481df-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="481df-183">Monitorowanie zmian w buforowanym pliku</span><span class="sxs-lookup"><span data-stu-id="481df-183">Monitor cached file changes</span></span>

<span data-ttu-id="481df-184">Zawartość pliku można buforować w <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>pamięci za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="481df-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="481df-185">Buforowanie w pamięci jest opisane w [pamięci podręcznej w pamięci](xref:performance/caching/memory) tematu.</span><span class="sxs-lookup"><span data-stu-id="481df-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="481df-186">Bez podejmowania dodatkowych kroków, takich jak implementacja opisana poniżej, *przestarzałe* (nieaktualne) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe ulegną zmianie.</span><span class="sxs-lookup"><span data-stu-id="481df-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="481df-187">Na przykład nie biorąc pod uwagę stan buforowanego pliku źródłowego podczas [odnawiania okres wygaśnięcia przesuwne](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych pliku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="481df-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="481df-188">Każde żądanie danych odnawia przesuwany okres wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="481df-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="481df-189">Wszystkie funkcje aplikacji, które używają zawartości buforowanej pliku, mogą odbierać nieaktualną zawartość.</span><span class="sxs-lookup"><span data-stu-id="481df-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="481df-190">Za pomocą zmiany tokenów w scenariuszu buforowania plików zapobiega obecności starych zawartości pliku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="481df-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="481df-191">Przykładowa aplikacja demonstruje implementację podejścia.</span><span class="sxs-lookup"><span data-stu-id="481df-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="481df-192">Próbka wykorzystuje `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="481df-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="481df-193">Zwracanie zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="481df-193">Return file content.</span></span>
* <span data-ttu-id="481df-194">Zaimplementuj algorytm ponawiania próby z wykładniczym wycofywania w celu pokrycia przypadków, w których blokada pliku tymczasowo uniemożliwia odczyt pliku.</span><span class="sxs-lookup"><span data-stu-id="481df-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="481df-195">*Narzędzia/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="481df-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="481df-196">A `FileService` jest tworzony do obsługi wyszukiwania plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="481df-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="481df-197">Wywołanie `GetFileContent` metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci podręcznej i zwrócić ją do osoby dzwoniącej (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="481df-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="481df-198">Jeśli zawartość w pamięci podręcznej nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną podjęte następujące akcje:</span><span class="sxs-lookup"><span data-stu-id="481df-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="481df-199">Zawartość pliku jest `GetFileContent`uzyskiwana za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="481df-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="481df-200">Token zmiany jest uzyskiwany od dostawcy plików za pomocą [pliku IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="481df-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="481df-201">Wywołania zwrotnego tokenu jest wyzwalany, gdy plik jest modyfikowany.</span><span class="sxs-lookup"><span data-stu-id="481df-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="481df-202">Zawartość pliku jest buforowana z przesuwanym okresem [wygaśnięcia.](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)</span><span class="sxs-lookup"><span data-stu-id="481df-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="481df-203">Token zmiany jest dołączony do [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) do eksmisji wpis pamięci podręcznej, jeśli plik zmienia się, gdy jest buforowany.</span><span class="sxs-lookup"><span data-stu-id="481df-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="481df-204">W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji .</span><span class="sxs-lookup"><span data-stu-id="481df-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="481df-205">`IWebHostEnvironment.ContentRootFileProvider`służy do uzyskania <xref:Microsoft.Extensions.FileProviders.IFileProvider> punktu na aplikacji `IWebHostEnvironment.ContentRootPath`.</span><span class="sxs-lookup"><span data-stu-id="481df-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="481df-206">Jest `filePath` uzyskiwany za pomocą [pliku IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="481df-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="481df-207">Jest `FileService` zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.</span><span class="sxs-lookup"><span data-stu-id="481df-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="481df-208">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="481df-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="481df-209">Model strony ładuje zawartość pliku przy użyciu usługi.</span><span class="sxs-lookup"><span data-stu-id="481df-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="481df-210">W `OnGet` metodzie strony Indeks *(Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="481df-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="481df-211">CompositeChangeToken, klasa</span><span class="sxs-lookup"><span data-stu-id="481df-211">CompositeChangeToken class</span></span>

<span data-ttu-id="481df-212">Aby reprezentować jedno `IChangeToken` lub więcej wystąpień w <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> jednym obiekcie, należy użyć klasy.</span><span class="sxs-lookup"><span data-stu-id="481df-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="481df-213">`HasChanged`w raportach `true` tokenu złożonego, `true`jeśli jest reprezentowany token `HasChanged` .</span><span class="sxs-lookup"><span data-stu-id="481df-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="481df-214">`ActiveChangeCallbacks`w raportach `true` tokenu złożonego, `true`jeśli jest reprezentowany token `ActiveChangeCallbacks` .</span><span class="sxs-lookup"><span data-stu-id="481df-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="481df-215">Jeśli wystąpi wiele zdarzeń zmiany równoczesnych, wywołania zwrotnego zmiany złożonej jest wywoływana jeden raz.</span><span class="sxs-lookup"><span data-stu-id="481df-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="481df-216">*Token zmiany* jest uniwersalnym, niskopoziomowym blokiem konstrukcyjnym używanym do śledzenia zmian stanu.</span><span class="sxs-lookup"><span data-stu-id="481df-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="481df-217">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="481df-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="481df-218">Interfejs IChangeToken</span><span class="sxs-lookup"><span data-stu-id="481df-218">IChangeToken interface</span></span>

<span data-ttu-id="481df-219"><xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="481df-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="481df-220">`IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> obszarze nazw.</span><span class="sxs-lookup"><span data-stu-id="481df-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="481df-221">W przypadku aplikacji, które nie używają [metapakietu Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)utwórz odwołanie do pakietu [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="481df-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="481df-222">`IChangeToken`posiada dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="481df-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="481df-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskazać, czy token proaktywnie wywołuje wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="481df-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="481df-224">Jeśli `ActiveChangedCallbacks` jest `false`ustawiona na , wywołanie zwrotne `HasChanged` nigdy nie jest wywoływane, a aplikacja musi sondować zmiany.</span><span class="sxs-lookup"><span data-stu-id="481df-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="481df-225">Jest również możliwe dla tokenu nigdy nie zostać anulowane, jeśli nie nastąpią żadne zmiany lub odbiornika zmian podstawowych jest usuwany lub wyłączony.</span><span class="sxs-lookup"><span data-stu-id="481df-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="481df-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>otrzymuje wartość, która wskazuje, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="481df-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="481df-227">Interfejs `IChangeToken` zawiera [RegisterChangeCallback(Action\<Object>, Object),](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) który rejestruje wywołanie zwrotne, który jest wywoływany po zmianie tokenu.</span><span class="sxs-lookup"><span data-stu-id="481df-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="481df-228">`HasChanged`należy ustawić przed wywołaniem wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="481df-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="481df-229">ChangeToken, klasa</span><span class="sxs-lookup"><span data-stu-id="481df-229">ChangeToken class</span></span>

<span data-ttu-id="481df-230"><xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną używaną do propagowania powiadomień o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="481df-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="481df-231">`ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> obszarze nazw.</span><span class="sxs-lookup"><span data-stu-id="481df-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="481df-232">W przypadku aplikacji, które nie używają [metapakietu Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)utwórz odwołanie do pakietu [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="481df-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="481df-233">[ChangeToken.OnChange(Func\<IChangeToken>, akcja)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywołać do każdej zmiany tokenu:</span><span class="sxs-lookup"><span data-stu-id="481df-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="481df-234">`Func<IChangeToken>`tworzy żeton.</span><span class="sxs-lookup"><span data-stu-id="481df-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="481df-235">`Action`jest wywoływana, gdy zmienia się token.</span><span class="sxs-lookup"><span data-stu-id="481df-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="481df-236">Przeciążenie [\<ChangeToken.OnChange TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest `Action`przekazywany do konsumenta tokenu .</span><span class="sxs-lookup"><span data-stu-id="481df-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="481df-237">`OnChange`zwraca <xref:System.IDisposable>plik .</span><span class="sxs-lookup"><span data-stu-id="481df-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="481df-238">Wywołanie <xref:System.IDisposable.Dispose*> zatrzymuje tokenu od nasłuchiwania dalszych zmian i zwalnia zasoby tokenu.</span><span class="sxs-lookup"><span data-stu-id="481df-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="481df-239">Przykładowe zastosowania tokenów zmian w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="481df-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="481df-240">Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian w obiektach:</span><span class="sxs-lookup"><span data-stu-id="481df-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="481df-241">Do monitorowania zmian <xref:Microsoft.Extensions.FileProviders.IFileProvider>w <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> plikach, `IChangeToken` 's metoda tworzy dla określonych plików lub folderu do oglądania.</span><span class="sxs-lookup"><span data-stu-id="481df-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="481df-242">`IChangeToken`tokeny mogą być dodawane do wpisów pamięci podręcznej, aby wyzwolić eksmisje pamięci podręcznej na zmiany.</span><span class="sxs-lookup"><span data-stu-id="481df-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="481df-243">W `TOptions` przypadku zmian <xref:Microsoft.Extensions.Options.OptionsMonitor`1> domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma przeciążenie, które akceptuje jedno lub więcej <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="481df-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="481df-244">Każde wystąpienie `IChangeToken` zwraca, aby zarejestrować wywołanie zwrotne powiadomienia o zmianie dla zmian opcji śledzenia.</span><span class="sxs-lookup"><span data-stu-id="481df-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="481df-245">Monitoruj zmiany konfiguracji</span><span class="sxs-lookup"><span data-stu-id="481df-245">Monitor for configuration changes</span></span>

<span data-ttu-id="481df-246">Domyślnie ASP.NET szablonów Core używają [plików konfiguracyjnych JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.json*i *appsettings. Production.json*), aby załadować ustawienia konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="481df-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="481df-247">Pliki te są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean),](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="481df-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="481df-248">`reloadOnChange`wskazuje, czy konfiguracja powinna zostać ponownie załadowana przy zmianach w pliku.</span><span class="sxs-lookup"><span data-stu-id="481df-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="481df-249">To ustawienie pojawia <xref:Microsoft.AspNetCore.WebHost> się <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>w metodzie wygody:</span><span class="sxs-lookup"><span data-stu-id="481df-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="481df-250">Konfiguracja oparta na <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>plikach jest reprezentowana przez program .</span><span class="sxs-lookup"><span data-stu-id="481df-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="481df-251">`FileConfigurationSource`do <xref:Microsoft.Extensions.FileProviders.IFileProvider> monitorowania plików.</span><span class="sxs-lookup"><span data-stu-id="481df-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="481df-252">Domyślnie `IFileMonitor` jest dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>program , <xref:System.IO.FileSystemWatcher> który służy do monitorowania zmian w pliku konfiguracyjnym.</span><span class="sxs-lookup"><span data-stu-id="481df-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="481df-253">Przykładowa aplikacja demonstruje dwie implementacje do monitorowania zmian konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="481df-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="481df-254">Jeśli którykolwiek z *appsettings* plików zmiany, zarówno implementacje monitorowania plików wykonać kod&mdash;niestandardowy przykładowy aplikacja zapisuje komunikat do konsoli.</span><span class="sxs-lookup"><span data-stu-id="481df-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="481df-255">Plik konfiguracyjny `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenu dla zmiany pliku pojedynczej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="481df-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="481df-256">Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz po wyzwoleniu wielu wywołań zwrotnych tokenu, implementacja przykładu sprawdza skróty plików.</span><span class="sxs-lookup"><span data-stu-id="481df-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="481df-257">W przykładzie użyto mieszania plików SHA1.</span><span class="sxs-lookup"><span data-stu-id="481df-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="481df-258">Ponowna próba jest implementowana z wykładniczym wycofywania.</span><span class="sxs-lookup"><span data-stu-id="481df-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="481df-259">Ponowna próby jest obecny, ponieważ może wystąpić blokowanie plików, które tymczasowo uniemożliwia obliczanie nowego skrótu w pliku.</span><span class="sxs-lookup"><span data-stu-id="481df-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="481df-260">*Narzędzia/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="481df-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="481df-261">Prosty token zmiany uruchamiania</span><span class="sxs-lookup"><span data-stu-id="481df-261">Simple startup change token</span></span>

<span data-ttu-id="481df-262">Zarejestruj wywołania `Action` zwrotne konsumenta tokenu dla powiadomień o zmianie do tokenu ponownego ładowania konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="481df-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="481df-263">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="481df-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="481df-264">`config.GetReloadToken()`zapewnia token.</span><span class="sxs-lookup"><span data-stu-id="481df-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="481df-265">Wywołanie zwrotne `InvokeChanged` jest metodą:</span><span class="sxs-lookup"><span data-stu-id="481df-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="481df-266">Wywołanie zwrotne jest używany do przekazywania `state` w `IHostingEnvironment`programie , który jest przydatny do określenia pliku konfiguracji poprawne *appsettings* do monitorowania (na przykład *appsettings. Development.json,* gdy w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="481df-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="481df-267">Skróty plików są używane, `WriteConsole` aby zapobiec wiele razy uruchamianie instrukcji z powodu wielu wywołań zwrotnych tokenu, gdy plik konfiguracji zmienił się tylko raz.</span><span class="sxs-lookup"><span data-stu-id="481df-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="481df-268">Ten system działa tak długo, jak aplikacja jest uruchomiona i nie może być wyłączona przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="481df-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="481df-269">Monitorowanie zmian konfiguracji jako usługi</span><span class="sxs-lookup"><span data-stu-id="481df-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="481df-270">Przykład implementuje:</span><span class="sxs-lookup"><span data-stu-id="481df-270">The sample implements:</span></span>

* <span data-ttu-id="481df-271">Podstawowe monitorowanie tokenu uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="481df-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="481df-272">Monitorowanie jako usługa.</span><span class="sxs-lookup"><span data-stu-id="481df-272">Monitoring as a service.</span></span>
* <span data-ttu-id="481df-273">Mechanizm włączania i wyłączania monitorowania.</span><span class="sxs-lookup"><span data-stu-id="481df-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="481df-274">Próbka ustanawia `IConfigurationMonitor` interfejs.</span><span class="sxs-lookup"><span data-stu-id="481df-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="481df-275">*Rozszerzenia/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="481df-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="481df-276">Konstruktor zaimplementowana `ConfigurationMonitor`klasa, rejestruje wywołanie zwrotne dla powiadomień o zmianie:</span><span class="sxs-lookup"><span data-stu-id="481df-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="481df-277">`config.GetReloadToken()`dostarcza żeton.</span><span class="sxs-lookup"><span data-stu-id="481df-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="481df-278">`InvokeChanged`jest metodą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="481df-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="481df-279">W `state` tym przypadku jest odwołanie `IConfigurationMonitor` do wystąpienia, który jest używany do dostępu do stanu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="481df-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="481df-280">Używane są dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="481df-280">Two properties are used:</span></span>

* <span data-ttu-id="481df-281">`MonitoringEnabled`&ndash; Wskazuje, czy wywołanie zwrotne należy uruchomić jego kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="481df-281">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="481df-282">`CurrentState`&ndash; Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="481df-282">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="481df-283">Metoda `InvokeChanged` jest podobna do wcześniejszego podejścia, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="481df-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="481df-284">Nie uruchamia swojego kodu, chyba że `MonitoringEnabled` jest `true`.</span><span class="sxs-lookup"><span data-stu-id="481df-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="481df-285">Wyprowadza prąd `state` w `WriteConsole` jego wyjściu.</span><span class="sxs-lookup"><span data-stu-id="481df-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="481df-286">Wystąpienie `ConfigurationMonitor` jest rejestrowane jako `Startup.ConfigureServices`usługa w:</span><span class="sxs-lookup"><span data-stu-id="481df-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="481df-287">Strona Indeks oferuje kontrolę użytkownika nad monitorowaniem konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="481df-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="481df-288">Wystąpienie `IConfigurationMonitor` jest wstrzykiwane `IndexModel`do pliku .</span><span class="sxs-lookup"><span data-stu-id="481df-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="481df-289">*Strony/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="481df-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="481df-290">Monitor konfiguracji`_monitor`( ) służy do włączania lub wyłączania monitorowania i ustawiania bieżącego stanu sprzężenia zwrotnego interfejsu użytkownika:</span><span class="sxs-lookup"><span data-stu-id="481df-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="481df-291">Po `OnPostStartMonitoring` wyzwoleniu monitorowanie jest włączone, a bieżący stan jest czyszczony.</span><span class="sxs-lookup"><span data-stu-id="481df-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="481df-292">Po `OnPostStopMonitoring` wyzwoleniu monitorowanie jest wyłączone, a stan jest ustawiony tak, aby odzwierciedlać, że monitorowanie nie występuje.</span><span class="sxs-lookup"><span data-stu-id="481df-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="481df-293">Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.</span><span class="sxs-lookup"><span data-stu-id="481df-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="481df-294">*Strony/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="481df-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="481df-295">Monitorowanie zmian w buforowanym pliku</span><span class="sxs-lookup"><span data-stu-id="481df-295">Monitor cached file changes</span></span>

<span data-ttu-id="481df-296">Zawartość pliku można buforować w <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>pamięci za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="481df-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="481df-297">Buforowanie w pamięci jest opisane w [pamięci podręcznej w pamięci](xref:performance/caching/memory) tematu.</span><span class="sxs-lookup"><span data-stu-id="481df-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="481df-298">Bez podejmowania dodatkowych kroków, takich jak implementacja opisana poniżej, *przestarzałe* (nieaktualne) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe ulegną zmianie.</span><span class="sxs-lookup"><span data-stu-id="481df-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="481df-299">Na przykład nie biorąc pod uwagę stan buforowanego pliku źródłowego podczas [odnawiania okres wygaśnięcia przesuwne](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych pliku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="481df-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="481df-300">Każde żądanie danych odnawia przesuwany okres wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="481df-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="481df-301">Wszystkie funkcje aplikacji, które używają zawartości buforowanej pliku, mogą odbierać nieaktualną zawartość.</span><span class="sxs-lookup"><span data-stu-id="481df-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="481df-302">Za pomocą zmiany tokenów w scenariuszu buforowania plików zapobiega obecności starych zawartości pliku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="481df-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="481df-303">Przykładowa aplikacja demonstruje implementację podejścia.</span><span class="sxs-lookup"><span data-stu-id="481df-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="481df-304">Próbka wykorzystuje `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="481df-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="481df-305">Zwracanie zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="481df-305">Return file content.</span></span>
* <span data-ttu-id="481df-306">Zaimplementuj algorytm ponawiania próby z wykładniczym wycofywania w celu pokrycia przypadków, w których blokada pliku tymczasowo uniemożliwia odczyt pliku.</span><span class="sxs-lookup"><span data-stu-id="481df-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="481df-307">*Narzędzia/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="481df-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="481df-308">A `FileService` jest tworzony do obsługi wyszukiwania plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="481df-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="481df-309">Wywołanie `GetFileContent` metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci podręcznej i zwrócić ją do osoby dzwoniącej (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="481df-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="481df-310">Jeśli zawartość w pamięci podręcznej nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną podjęte następujące akcje:</span><span class="sxs-lookup"><span data-stu-id="481df-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="481df-311">Zawartość pliku jest `GetFileContent`uzyskiwana za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="481df-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="481df-312">Token zmiany jest uzyskiwany od dostawcy plików za pomocą [pliku IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="481df-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="481df-313">Wywołania zwrotnego tokenu jest wyzwalany, gdy plik jest modyfikowany.</span><span class="sxs-lookup"><span data-stu-id="481df-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="481df-314">Zawartość pliku jest buforowana z przesuwanym okresem [wygaśnięcia.](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)</span><span class="sxs-lookup"><span data-stu-id="481df-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="481df-315">Token zmiany jest dołączony do [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) do eksmisji wpis pamięci podręcznej, jeśli plik zmienia się, gdy jest buforowany.</span><span class="sxs-lookup"><span data-stu-id="481df-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="481df-316">W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji .</span><span class="sxs-lookup"><span data-stu-id="481df-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="481df-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) służy do <xref:Microsoft.Extensions.FileProviders.IFileProvider> uzyskania wskazując na aplikacji <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span><span class="sxs-lookup"><span data-stu-id="481df-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="481df-318">Jest `filePath` uzyskiwany za pomocą [pliku IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="481df-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="481df-319">Jest `FileService` zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.</span><span class="sxs-lookup"><span data-stu-id="481df-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="481df-320">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="481df-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="481df-321">Model strony ładuje zawartość pliku przy użyciu usługi.</span><span class="sxs-lookup"><span data-stu-id="481df-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="481df-322">W `OnGet` metodzie strony Indeks *(Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="481df-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="481df-323">CompositeChangeToken, klasa</span><span class="sxs-lookup"><span data-stu-id="481df-323">CompositeChangeToken class</span></span>

<span data-ttu-id="481df-324">Aby reprezentować jedno `IChangeToken` lub więcej wystąpień w <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> jednym obiekcie, należy użyć klasy.</span><span class="sxs-lookup"><span data-stu-id="481df-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="481df-325">`HasChanged`w raportach `true` tokenu złożonego, `true`jeśli jest reprezentowany token `HasChanged` .</span><span class="sxs-lookup"><span data-stu-id="481df-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="481df-326">`ActiveChangeCallbacks`w raportach `true` tokenu złożonego, `true`jeśli jest reprezentowany token `ActiveChangeCallbacks` .</span><span class="sxs-lookup"><span data-stu-id="481df-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="481df-327">Jeśli wystąpi wiele zdarzeń zmiany równoczesnych, wywołania zwrotnego zmiany złożonej jest wywoływana jeden raz.</span><span class="sxs-lookup"><span data-stu-id="481df-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="481df-328">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="481df-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
